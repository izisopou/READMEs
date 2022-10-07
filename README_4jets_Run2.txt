=== CMSSW Setup =====

1.Setup Release + Download from github combine+DijetAnalyzer

(Directions for currently recommended CMSSW release & tag can be found here: https://cms-analysis.github.io/HiggsAnalysis-CombinedLimit/ )

cd to where you want to setup your CMSSW release and do the following:


export SCRAM_ARCH=slc7_amd64_gcc700
cmsrel CMSSW_10_2_13
cd CMSSW_10_2_13/src
cmsenv
git clone https://github.com/CMSDIJET/DijetRootTreeAnalyzer CMSDIJET/DijetRootTreeAnalyzer
git clone -b dijetpdf_102X https://github.com/RazorCMS/HiggsAnalysis-CombinedLimit HiggsAnalysis/CombinedLimit
cd HiggsAnalysis/CombinedLimit


cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit
git fetch origin
git checkout v8.2.0
scramv1 b clean; scramv1 b # always make a clean build


# Get CombineHarvester - needed for Grid submissions

cd $CMSSW_BASE/src
git clone https://github.com/cms-analysis/CombineHarvester.git CombineHarvester
scram b

2. Now setup the RooParametricShapeBinPdfs needed for the analysis:

cd $CMSSW_BASE/src
cmsenv
cp /afs/cern.ch/user/d/dntounis/public/RooParametricShapeBinPdf_classes/February_2021/classes/cc_files/RooDijet5ParamBinPdf.cc HiggsAnalysis/CombinedLimit/src
cp /afs/cern.ch/user/d/dntounis/public/RooParametricShapeBinPdf_classes/February_2021/classes/cc_files/RooModDijet5ParamBinPdf.cc HiggsAnalysis/CombinedLimit/src

cp /afs/cern.ch/user/d/dntounis/public/RooParametricShapeBinPdf_classes/February_2021/classes/h_files/RooDijet5ParamBinPdf.h HiggsAnalysis/CombinedLimit/interface
cp /afs/cern.ch/user/d/dntounis/public/RooParametricShapeBinPdf_classes/February_2021/classes/h_files/RooModDijet5ParamBinPdf.h HiggsAnalysis/CombinedLimit/interface

cp /afs/cern.ch/user/d/dntounis/public/RooParametricShapeBinPdf_classes/February_2021/classes/cc_files/RooAtlas5ParamBinPdf.cc
 HiggsAnalysis/CombinedLimit/src
cp /afs/cern.ch/user/d/dntounis/public/RooParametricShapeBinPdf_classes/February_2021/classes/h_files/RooAtlas5ParamBinPdf.h HiggsAnalysis/CombinedLimit/interface

cd HiggsAnalysis/CombinedLimit/src
In the HiggsAnalysis/CombinedLimit/src folder modify the following files:

--> classes.h       : add the following lines:  
                                                #include "HiggsAnalysis/CombinedLimit/interface/RooAtlas5ParamBinPdf.h"
                                                #include "HiggsAnalysis/CombinedLimit/interface/RooModDijet5ParamBinPdf.h"

--> classes_def.xml : add the following lines:  	
                                                        <class name="RooAtlas5ParamBinPdf" /> 
                                                        <class name="RooModDijet5ParamBinPdf" /> 

cd back to CMSSW_xx_x_xx/src/HiggsAnalysis/CombinedLimit and make a clean build:
 
cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit 
scramv1 b clean; scramv1 b


=== Transfer files needed for the Fourjet analysis ===

cd $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/python

cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/scripts/BinnedFit_4J.py .
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/scripts/WriteDataCard_4J_envelope_Jim.py .

cd $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/config

cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/scripts/fourjet_alpha1_multipdf_3_func.config .
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/scripts/fourjet_alpha1_dijet_only.config .

==============================================================================================================================

1.To perform Background Only Fit:

python python/BinnedFit_4J.py -c config/fourjet_alpha1_dijet_only.config -l 137000 rootfiles/Data_Histo.root -b PFDijet2017_4J -d output/ --fit-spectrum

fourjet_alpha1_dijet_only.config is where you determine the range of fit, initial parameters etc (you will need different configs for each alpha slice so that the parameters are uncorrelated). In this particular config you fit with the dijet function (you can change the "combine_pdfs" if you want to fit with atlas or moddijet)
Data_Histo.root is the input data root file that contains the 1GeV histo
-d output/ is the output directory

This step produces the bkg only fit plot and a root file DijetFitResults_PFDijet2017_4J.root that contains the parameters of the background only fit

==============================================================================================================================

2. To create the datacard:

python python/WriteDataCard_4J_envelope_Jim.py -b PFDijet2017_4J -c config/fourjet_alpha1_multipdf_3_func.config  -m qq --mass 8400 -i output/DijetFitResults_PFDijet2017_4J.root rootfiles/Data_Histo.root rootfiles/Signal_Histo.root --jesUp rootfiles/Signal_Histo_JESup.root  --jesDown rootfiles/Signal_Histo_JESdown.root  --jerUp rootfiles/Signal_Histo_JERup.root --jerDown rootfiles/Signal_Histo.root -d output/ --xsec 0.01 --lumi 137000 

This step produces the datacard (one .txt and one .root file, .txt is the datacard) using the DijetFitResults_PFDijet2017_4J.root from the previous step, and the Data and Signal 1GeV histos that are inside the respective root files. --xsec is the Reference cross section. 
The config file is different as the one when doing the background only fit. Here you use the fourjet_alpha1_multipdf_3_func.config that implements the envelope method for the S+B fit. Again, for different alpha slices you will need different configs to keep the parameters uncorrelated.

==============================================================================================================================

3. To calculate Observed Limits-Significance

combine -M AsymptoticLimits  output/card.txt -n _name --setParameterRanges r=0,2. --saveWorkspace --cminDefaultMinimizerTolerance 0.00100 --cminDefaultMinimizerStrategy 2 --strictBounds

combine -M Significance output/card.txt --rMin 0 --rMax 2 -n test_name
 
==============================================================================================================================

4. FitDiagnostics

combine -M FitDiagnostics --robustFit 1 higgsCombine_name.AsymptoticLimits.mH120.root --rMin 0 --rMax 2 -n _name --setCrossingTolerance 0.00001


This command produces a root file.
To open it:

cmsenv
root -l
TFile *f = new TFile("fitDiagnostics_name.root");
f->cd();
f->ls();
RooFitResult *s = (RooFitResult*)f->Get("fit_s"); 
fit_s->Print();

Then it will print the parameters of the S+B fit and the best fit r, best fit jer, jes etc.                

==============================================================================================================================







