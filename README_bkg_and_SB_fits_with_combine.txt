Instructions on how to perform bkg fits with combine

(1) Setup Release + Download from github combine+DijetAnalyzer


mkdir 4jets_Combine/	(or however you want to name your folder)
cd 4jets_Combine/
cmsrel CMSSW_10_2_13
cd CMSSW_10_2_13/src
cmsenv
git clone https://github.com/CMSDIJET/DijetRootTreeAnalyzer CMSDIJET/DijetRootTreeAnalyzer
git clone -b dijetpdf_102X https://github.com/RazorCMS/HiggsAnalysis-CombinedLimit HiggsAnalysis/CombinedLimit
cd HiggsAnalysis/CombinedLimit
scramv1 b clean; scramv1 b    (this command makes a clean build)

There should be no errors executing the above command.


(2) Now setup the RooParametricShapeBinPdfs needed for the analysis (Dijet, ATLAS and Moddijet functions):

Take the following files from Dimitris' directories (he also has more functions other than those 3 if interested):

From /afs/cern.ch/user/d/dntounis/public/RooParametricShapeBinPdf_classes/February_2021/classes/cc_files/ take RooDijet5ParamBinPdf.cc, RooModDijet5ParamBinPdf.cc and RooAtlas5ParamBinPdf.cc and copy them inside HiggsAnalysis/CombinedLimit/src in your setup.

From /afs/cern.ch/user/d/dntounis/public/RooParametricShapeBinPdf_classes/February_2021/classes/h_files/ take RooDijet5ParamBinPdf.h, RooModDijet5ParamBinPdf.h and RooAtlas5ParamBinPdf.h and copy them inside HiggsAnalysis/CombinedLimit/interface

In the HiggsAnalysis/CombinedLimit/src folder modify the following files:

--> classes.h       : add the following lines:  
                                                #include "HiggsAnalysis/CombinedLimit/interface/RooAtlas5ParamBinPdf.h"
                                                #include "HiggsAnalysis/CombinedLimit/interface/RooModDijet5ParamBinPdf.h"

--> classes_def.xml : add the following lines:  	
                                                        <class name="RooAtlas5ParamBinPdf" /> 
                                                        <class name="RooModDijet5ParamBinPdf" /> 

cd back to CMSSW_10_2_13/src/HiggsAnalysis/CombinedLimit and make a clean build:
 
cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit 
scramv1 b clean; scramv1 b

There should be no errors executing the above command.


(3) Take the most recent files needed to fit for the fourjet analysis

From /afs/cern.ch/user/i/izisopou/public/ForAmalia/Combine_codes/ take the following files

BinnedFit_4J.py, WriteDataCard_4J.py and put them inside CMSDIJET/DijetRootTreeAnalyzer/python/

fourjet_alpha*_dijet_only.config and put them inside CMSDIJET/DijetRootTreeAnalyzer/config/

From /afs/cern.ch/user/i/izisopou/public/ForAmalia/4jets_Data_Histos/ take all data histos and put them inside CMSDIJET/DijetRootTreeAnalyzer/inputs/



(4) Perform Background Only Fit:

python python/BinnedFit_4J.py -c config/fourjet_alpha8_dijet_only.config -l 137000 inputs/HISTOS_4J_Alldata_SR_alphabin8.root -b PFDijet2017_4J_alphabin8 -d output/ --fit-spectrum

Explanation of the command:

The BinnedFit_4J.py script is the one that performs the background only fit. 

Inside the fourjet_alpha8_dijet_only.config you give the initial parameters (line 10). If you give the value 0 to p2,p3 or p4 then the code will automattically fix them to 0. This is done inside the WriteDataCard_4J.py in lines 66-71. This code is being called by BinnedFit_4J.py in line 8 of BinnedFit_4J.py.

Inside the fourjet_alpha8_dijet_only.config you also determine the range of the fit and the binning.

Inside the fourjet_alpha8_dijet_only.config you also determine with which function to fit. At this moment the dijet function is used (line 12) but you can also change to the atlas function (line 16) or moddijet function (line 19) which are commented out for now (with a hashtag in the beginning of the line). Better to create similar fourjet_alpha8_atlas_only.config and fourjet_alpha8_moddijet_only.config files for convenience.

Inside the fourjet_alpha8_dijet_only.config you also give the name of the input data histo in line 5, which is inside inputs/HISTOS_4J_Alldata_SR_alphabin8.root



This command produces inside the output/ directory the bkg only fit plot and a root file DijetFitResults_PFDijet2017_4J_alphabin8.root that contains the parameters of the background only fit (they are also printed when you execute the command). To view the parameters do in the terminal:

root -l
TFile *f = new TFile("DijetFitResults_PFDijet2017_4J_alphabin8.root")
f->ls()
wPFDijet2017_4J_alphabin8->cd()
f->ls()
p1_PFDijet2017_4J_alphabin8->Print()
p2_PFDijet2017_4J_alphabin8->Print()
p3_PFDijet2017_4J_alphabin8->Print()
p4_PFDijet2017_4J_alphabin8->Print()



When you have not splitted data in 13 alpha bin but have them altogether inclusively, you can perform the background only fit with the same way. The data histo is located here: /afs/cern.ch/user/i/izisopou/public/ForAmalia/4jets_Data_Histos/HISTOS_4J_Alldata_SR_alphagt0p1.root (if you add all the 13 histograms with the alpha bins you get this one) and the config file here: /afs/cern.ch/user/i/izisopou/public/ForAmalia/Combine_codes/fourjet_inclusive_dijet_only.config

Note: For the inclusive case we start the fit at 1530 GeV and use the 5-parameter function (Fit probabilities and fisher test has told us it is better than the 3 and 4-parameter ones)


(5) Create a datacard (i.e. perform a S+B fit)

First of all, all signal histograms are located here: /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets/signals_13bins/
As a test you can perform the S+B fit with, lets say, the signal with Suu mass of 8400 GeV and chi mass of 2100 GeV (alpha_true=M(chi)/M(Suu)=0.25). These are located here:

/eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets/signals_13bins/alpha0p25/nominal/ResonanceShapes_Suu8400_Chi2100_SR_alphabin*.root
/eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets/signals_13bins/alpha0p25/nominal/ResonanceShapes_Suu8400_Chi2100_SR_noslices.root
/eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets/signals_13bins/alpha0p25/jes_up/ResonanceShapes_Suu8400_Chi2100_SR_alphabin*.root
/eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets/signals_13bins/alpha0p25/jes_up/ResonanceShapes_Suu8400_Chi2100_SR_noslices.root
/eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets/signals_13bins/alpha0p25/jes_down/ResonanceShapes_Suu8400_Chi2100_SR_alphabin*.root
/eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets/signals_13bins/alpha0p25/jes_down/ResonanceShapes_Suu8400_Chi2100_SR_noslices.root
/eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets/signals_13bins/alpha0p25/jer_up/ResonanceShapes_Suu8400_Chi2100_SR_alphabin*.root
/eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets/signals_13bins/alpha0p25/jer_up/ResonanceShapes_Suu8400_Chi2100_SR_noslices.root

You can either copy whichever you want inside your inputs/ directory or just give the full path above when running the commands.


--> When you have one inclusive spectrum to fit (i.e. you don't have many alpha bins). 

python python/WriteDataCard_4J.py -b PFDijet2017_4J -c config/fourjet_inclusive_dijet_only.config -m qq --mass 8400 -i output/DijetFitResults_PFDijet2017_4J.root inputs/HISTOS_4J_Alldata_SR_alphagt0p1.root inputs/Signal_Histo_noslices.root --jesUp inputs/Signal_Histo_noslices_JESup.root  --jesDown inputs/Signal_Histo_noslices_JESdown.root  --jerUp inputs/Signal_Histo_noslices_JERup.root --jerDown inputs/Signal_Histo_noslices.root -d output/ --xsec 0.1 --lumi 137000 

Explanation of each option:

-b PFDijet2017_4J : just a naming convention in the config and the output file

-c config/fourjet_inclusive_dijet_only.config : the config file

-m qq : It doesn't really matter, you just choose qq, gg or qg for naming purposes of the output

--mass 8400 : Again for naming purposes of the output this is the Suu mass of the signal you are fitting

-i output/DijetFitResults_PFDijet2017_4J.root : This is an optional command. Instead of taking as initial parameters of the S+B fit the ones you have written in the config file it takes the post-fit parameters of the background only fit (it helps because you do not expect vastly different parameters between the background and S+B fits). This root file is the output file of the background only fit you performed previously.

inputs/HISTOS_4J_Alldata_SR_alphagt0p1.root : the data histogram

inputs/Signal_Histo_noslices.root : similarly as the data histo, this is the (nominal) signal histogram

--jesUp inputs/Signal_Histo_noslices_JESup.root : the signal histogram that has been shifted in the x axis by +2%

--jesDown inputs/Signal_Histo_noslices_JESdown.root : the signal histogram that has been shifted in the x axis by -2%

--jerUp inputs/Signal_Histo_noslices_JERup.root : the signal histogram that has a worse resolution than the nominal by 10%

--jerDown inputs/Signal_Histo_noslices.root : the nominal signal histogram. We do not have a jerDown histogram as we do not improve the resolution

-d output/ : output directory where the datacard will be written

--xsec 0.1 : this is a reference cross-section (xsec) in picobarns (pb). You give whatever you want here but you need to remember this number! Because the datacard is interconnected with this number from now on. All the commands in combine with this datacard that will give as output a xsec number, it will not be in pb, but will be a dimensionless number that will have to be multiplied with this reference xsec to be converted to pb

--lumi 137000 : the integrated luminosity of your data


This command creates the datacard (.txt and .root files).



---> When you perform a S+B fit in alpha bins.

You do the same as before for each alpha bin separately (giving the corresponding config and root files), so you will have to create 13 different datacards:

python python/WriteDataCard_4J.py -b PFDijet2017_4J_alphabin8 -c config/fourjet_alpha8_dijet_only.config -m qq --mass 8400 -i output/DijetFitResults_PFDijet2017_4J_alphabin8.root inputs/HISTOS_4J_Alldata_SR_alphabin8.root inputs/Signal_Histo_alphabin8.root --jesUp inputs/Signal_Histo_alphabin8_JESup.root  --jesDown inputs/Signal_Histo_alphabin8_JESdown.root  --jerUp inputs/Signal_Histo_alphabin8_JERup.root --jerDown inputs/Signal_Histo_alphabin8.root -d output/ --xsec 0.1*frac --lumi 137000


IMPORTANT: This is a simultaneous S+B fit across the 13 alpha bins. This means that you will have to determine ONE xsec value for the signal (lets say 0.1pb), and for each alpha bin you give as --xsec the fraction of this 0.1pb that you expect to have in this particular alpha bin, so that in the end all 13 values add up to 0.1pb. Basically you will have to know how the signal distributes among the 13 alpha bins to see how much each bin contributes to the S+B fit. Simple example: If you expect to have 60% of your signal in alpha bin 8, 20% in alpha bin 7 and 20% in alpha bin 9 then for all other slices you give --xsec 0 , for bins 7 and 9 you give --xsec 0.02 and for bin 8 you give --xsec 0.06.

The percentages for the 13 alpha bins for signals with alpha_true=M(chi)/M(Suu)=0.25 are roughly the following:

alphabin1-> 0%
alphabin2-> 1%
alphabin3-> 1%
alphabin4-> 1%
alphabin5-> 2%
alphabin6-> 3%
alphabin7-> 13%
alphabin8-> 51%
alphabin9-> 15%
alphabin10-> 5%
alphabin11-> 3%
alphabin12-> 3%
alphabin13-> 3%


Now that you have created the 13 datacards you will have to combine them in one datacard with the following command:

combineCards.py dijet_combine_Suu_8400_lumi-137.000_PFDijet2017_4J_alphabin1.txt dijet_combine_Suu_8400_lumi-137.000_PFDijet2017_4J_alphabin2.txt dijet_combine_Suu_8400_lumi-137.000_PFDijet2017_4J_alphabin3.txt dijet_combine_Suu_8400_lumi-137.000_PFDijet2017_4J_alphabin4.txt dijet_combine_Suu_8400_lumi-137.000_PFDijet2017_4J_alphabin5.txt dijet_combine_Suu_8400_lumi-137.000_PFDijet2017_4J_alphabin6.txt dijet_combine_Suu_8400_lumi-137.000_PFDijet2017_4J_alphabin7.txt dijet_combine_Suu_8400_lumi-137.000_PFDijet2017_4J_alphabin8.txt dijet_combine_Suu_8400_lumi-137.000_PFDijet2017_4J_alphabin9.txt dijet_combine_Suu_8400_lumi-137.000_PFDijet2017_4J_alphabin10.txt dijet_combine_Suu_8400_lumi-137.000_PFDijet2017_4J_alphabin11.txt dijet_combine_Suu_8400_lumi-137.000_PFDijet2017_4J_alphabin12.txt dijet_combine_Suu_8400_lumi-137.000_PFDijet2017_4J_alphabin13.txt > card_combined_8400.txt



To do the following steps cd inside DijetTreeAnalyzer/ :



(6) Calculate asymptotic upper cross section limits

combine -M AsymptoticLimits /path/to/datacard.txt  --setParameterRanges r=0,5 --saveWorkspace --cminDefaultMinimizerTolerance 0.00100 --cminDefaultMinimizerStrategy 2 --strictBounds

This prints the observed limit, and the expected limit (and the +/- 1sigma, 2sigma) in 95% CL. It also creates a root file with the numbers saved. All the output limit values are dimensionless and should be multiplied with the reference xsec to indicate xsec in pb. 

r = signal strength (xsec of signal). The range of r is important, setting the max value too low or too high can break the fit and yield nonsense results. The max value should be roughly 5 times the +2sigma expected limit. The minimum value should be 0. The r variable in this command is a dimensionless number, rMax = 5 means 5 times the reference xsec with which the datacard is created.




(7) Calculate significance

combine -M Significance /path/to/datacard.txt --rMin 0 --rMax 5 --saveWorkspace

This prints the significance and also creates a root file where the number is saved.

--rMin 0 and --rMax 5 commands are exactly the same as --setParameterRanges r=0,5  : Choose whatever you want. The min, max values should be the same as the ones you have determined above to make sure you get stable results.




(8) Fit Diagnostics of datacard

combine -M FitDiagnostics --robustFit 1 /path/to/datacard.txt --rMin 0 --rMax 5 --setCrossingTolerance 0.00001 --cminDefaultMinimizerStrategy=0 

This one prints the best fit signal strength (r), i.e. how much signal we find when fitting with the S+B fit.

It also creates a root file that contains the post-fit parameters of the background component of the fit (normalization,p1,p2,p3,... parameters) and the post-fit parameters of the signal component of the fit, namely the best fit r, best fit JES, best fit JER etc. To view these do the following:


cmsenv
root -l
TFile *f = new TFile("fitDiagnostics.root");
f->cd();
f->ls();
RooFitResult *s = (RooFitResult*)f->Get("fit_s"); 
fit_s->Print();

