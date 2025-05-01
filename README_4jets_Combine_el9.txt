
================================================ Setup CMSSW and codes ================================================

Login to lxplus9 and create a folder. Inside it:

cmsrel CMSSW_14_1_0_pre4
cd CMSSW_14_1_0_pre4/src
cmsenv

1) Git clone CMSDIJET/DijetRootTreeAnalyzer

git clone -b PairedDijetAnalysis https://github.com/CMSDIJET/DijetRootTreeAnalyzer CMSDIJET/DijetRootTreeAnalyzer
(or git clone -b Run3 https://github.com/CMSDIJET/DijetRootTreeAnalyzer CMSDIJET/DijetRootTreeAnalyzer if you are working on the dijet or other analysis)
scram b -j 8

The scripts below should already be in the 'PairedDijetAnalysis' branch, but if you use another branch copy them to your directory:

From /afs/cern.ch/user/i/izisopou/public/Combine_Codes take BinnedFit_4jets.py, WriteDataCard_4jets.py, WriteDataCard_4jets_envelope.py, GetCombine_final.py, Plot1DLimit_final.py and Plot1DSignificance_final.py and copy them inside $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/python

From /afs/cern.ch/user/i/izisopou/public/Combine_Codes take Config.py and copy it inside $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/python/framework
From /afs/cern.ch/user/i/izisopou/public/Combine_Codes take Utils.py and copy it inside $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/python/rootTools

From /afs/cern.ch/user/i/izisopou/public/Combine_Codes take plot1DScan.py, plotGof.py and custom_crab.py and copy them inside $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer 

MAKE SURE that there are no __init__.py files in the directories:
$CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/python/
$CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/python/framework
$CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/python/rootTools
These __init__.py files are not needed in python3 anymore.


2) Git clone HiggsAnalysis/CombinedLimit 

git clone https://github.com/cms-analysis/HiggsAnalysis-CombinedLimit.git HiggsAnalysis/CombinedLimit 
cd HiggsAnalysis/CombinedLimit
git fetch origin
git checkout v10.0.0
scramv1 b clean; scramv1 b

From /afs/cern.ch/user/i/izisopou/public/Combine_Codes/fit_functions/ take RooDijet5ParamBinPdf.cc, RooModDijet5ParamBinPdf.cc and RooAtlas5ParamBinPdf.cc and copy them inside HiggsAnalysis/CombinedLimit/src in your setup.

From /afs/cern.ch/user/i/izisopou/public/Combine_Codes/fit_functions/ take RooDijet5ParamBinPdf.h, RooModDijet5ParamBinPdf.h and RooAtlas5ParamBinPdf.h and copy them inside HiggsAnalysis/CombinedLimit/interface

In the HiggsAnalysis/CombinedLimit/src folder modify the following files:

--> classes.h       : add the following lines:  
                                                #include "HiggsAnalysis/CombinedLimit/interface/RooDijet5ParamBinPdf.h"
                                                #include "HiggsAnalysis/CombinedLimit/interface/RooAtlas5ParamBinPdf.h"
                                                #include "HiggsAnalysis/CombinedLimit/interface/RooModDijet5ParamBinPdf.h"

--> classes_def.xml : add the following lines:  	
                                                        <class name="RooDijet5ParamBinPdf" />
                                                        <class name="RooAtlas5ParamBinPdf" /> 
                                                        <class name="RooModDijet5ParamBinPdf" /> 


cd back to $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit and make a clean build:
 
cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit 
scramv1 b clean; scramv1 b


3) Git clone CombineHarvester/

cd $CMSSW_BASE/src/
git clone https://github.com/cms-analysis/CombineHarvester.git CombineHarvester
cd CombineHarvester/
git fetch origin
git checkout v3.0.0-pre1
scramv1 b clean; scramv1 b

The following changes concern crab job submissions:

In HiggsAnalysis/CombinedLimit/python/tool_base/crab.py:
L.15    -> config.JobType.psetName = os.environ["CMSSW_BASE"] + "/src/CombineHarvester/CombineTools/scripts/do_nothing_cfg.py"
L.17-21 -> config.JobType.inputFiles = [
    os.environ["CMSSW_BASE"] + "/src/CombineHarvester/CombineTools/scripts/FrameworkJobReport.xml",
    os.environ["CMSSW_BASE"] + "/src/CombineHarvester/CombineTools/scripts/copyRemoteWorkspace.sh",
    os.environ["CMSSW_BASE"] + "/bin/" + os.environ["SCRAM_ARCH"] + "/combine",
]

In HiggsAnalysis/CombinedLimit/python/tool_base/CombineToolBase.py:
L.381 -> do_nothing_script = open(os.environ["CMSSW_BASE"] + "/src/CombineHarvester/CombineTools/scripts/do_nothing_cfg.py", "w")

.
.
.
.

After all the above go to $CMSSW_BASE/src/ and make a clean build:
scramv1 b clean; scramv1 b


From now on, main work directory is the $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer





================================================ Background only fits with the 3 different functions ================================================

For alpha bin ("$bin_No") = 1 up to 13:

python3 python/BinnedFit_4jets.py -c config/Configfiles_13_slices_dijet_only/run2_fourjet_alpha"$bin_No"_dijet_only_upto10072.config -l 137600 /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets_Run2/data_histos/HISTOS_4J_AlldataRun2_SR_alphabin"$bin_No".root -b PFDijetRun2_4J_alphabin"$bin_No" -d output/BkgOnlyFits/Dijet-3p/ --fit-spectrum

python3 python/BinnedFit_4jets.py -c config/Configfiles_13_slices_atlas_only/run2_fourjet_alpha"$bin_No"_atlas_only_upto10072.config -l 137600 /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets_Run2/data_histos/HISTOS_4J_AlldataRun2_SR_alphabin"$bin_No".root -b PFDijetRun2_4J_alphabin"$bin_No" -d output/BkgOnlyFits/Atlas-3p/ --fit-spectrum

python3 python/BinnedFit_4jets.py -c config/Configfiles_13_slices_moddijet_only/run2_fourjet_alpha"$bin_No"_moddijet_only_upto10072.config -l 137600 /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets_Run2/data_histos/HISTOS_4J_AlldataRun2_SR_alphabin"$bin_No".root -b PFDijetRun2_4J_alphabin"$bin_No" -d output/BkgOnlyFits/Moddijet-3p/ --fit-spectrum


EXPLANATION OF COMMAND:

The BinnedFit_4jets.py script is the one that performs the background only fit. 

Inside the run2_fourjet_alpha"$bin_No"_dijet_only_upto10072.config, run2_fourjet_alpha"$bin_No"_atlas_only_upto10072.config and run2_fourjet_alpha"$bin_No"_moddijet_only_upto10072.config:

-> you give the initial parameters. If you give the value 0 to p3 or p4 then the code will automattically fix them to 0. This is done inside the WriteDataCard_4ets.py in lines 58-61. This code is being called by BinnedFit_4jets.py in line 8 of BinnedFit_4jets.py.
-> you determine the range of the fit and the binning
-> you determine with which function to fit
-> you give the name of the input data histo in line 5, which is inside /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets_Run2/data_histos/HISTOS_4J_AlldataRun2_SR_alphabin"$bin_No".root
-> p0 should always be fixed to 1. The normalization is handled by the Ntot variable (Ntot*p0)

This command produces inside the output/ directory the bkg only fit plot and a root file DijetFitResults_PFDijetRun2_4J_alphabin"$bin_No".root that contains the parameters of the background only fit (they are also printed when you execute the command). To view the parameters do in the terminal:

root -l
TFile *f = new TFile("DijetFitResults_PFDijetRun2_4J_alphabin"$bin_No".root")
f->ls()
wPFDijetRun2_4J_alphabin"$bin_No"->cd()
f->ls()
Ntot_PFDijetRun2_4J_alphabin"$bin_No"_bkg->Print()
p1_PFDijetRun2_4J_alphabin"$bin_No"->Print()
p2_PFDijetRun2_4J_alphabin"$bin_No"->Print()
p3_PFDijetRun2_4J_alphabin"$bin_No"->Print()
p4_PFDijetRun2_4J_alphabin"$bin_No"->Print()



================================================ Create datacard ================================================

For alpha bin = 1 up to 13:

python3 python/WriteDataCard_4jets_envelope.py -b PFDijetRun2_4J_alphabin"$bin_No" -c config/Configfiles_13_slices_Envelope_3_functions/run2_fourjet_alpha"$bin_No"_multipdf_3_func_upto10072.config -m W-0p0043_Suu --model2 Chi --mass 8400 --mass2 2100 /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets_Run2/data_histos/HISTOS_4J_AlldataRun2_SR_alphabin"$bin_No".root /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets_Run2/Narrow_Resonance_Search/signals_13bins/alpha0p25/nominal/ResonanceShapes_Suu"$Suu"_Chi"$Chi"_SR_alphabin"$bin_No".root --jesUp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets_Run2/Narrow_Resonance_Search/signals_13bins/alpha0p25/jes_up/ResonanceShapes_Suu"$Suu"_Chi"$Chi"_SR_alphabin"$bin_No".root --jesDown /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets_Run2/Narrow_Resonance_Search/signals_13bins/alpha0p25/jes_down/ResonanceShapes_Suu"$Suu"_Chi"$Chi"_SR_alphabin"$bin_No".root --jerUp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets_Run2/Narrow_Resonance_Search/signals_13bins/alpha0p25/jer_up/ResonanceShapes_Suu"$Suu"_Chi"$Chi"_SR_alphabin"$bin_No".root --jerDown /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets_Run2/Narrow_Resonance_Search/signals_13bins/alpha0p25/nominal/ResonanceShapes_Suu"$Suu"_Chi"$Chi"_SR_alphabin"$bin_No".root -d output/datacards_alpha0p25_refxsec0p01pb/ --xsec $fraction --lumi 137600 --multi

EXPLANATION OF COMMAND:

-b PFDijetRun2_4J_alphabin"$bin_No" : just a naming convention in the config and the output file
-c config/Configfiles_13_slices_Envelope_3_functions/run2_fourjet_alpha"$bin_No"_multipdf_3_func_upto10072.config : the config file
-m W-0p0043_Suu : this is for naming purposes of the output datacard
--model2 Chi : this is for naming purposes of the output datacard
--mass 8400 --mass2 2100 : Again for naming purposes of the output this is the Suu and chi masses of the signal you are fitting
/eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets_Run2/data_histos/HISTOS_4J_AlldataRun2_SR_alphabin"$bin_No".root : the input data histogram (in 1GeV binning)
/eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets_Run2/Narrow_Resonance_Search/signals_13bins/alpha0p25/nominal/ResonanceShapes_Suu"$Suu"_Chi"$Chi"_SR_alphabin"$bin_No".root : the input signal histogram, its integral normalized to unity (in dijet standard binning or 1GeV binning -> it will automatically get rebinned to dijet standard binning)
--jesUp : the signal histogram where we have shifted the JEC factor by +1sigma of its uncertainty
--jesDown : the signal histogram where we have shifted the JEC factor by -1sigma of its uncertainty
--jerUp : the signal histogram that has a worse resolution than the nominal by ~10%
--jerDown : the nominal signal histogram. We do not have a jerDown histogram as we do not improve the resolution (resolution is already 10% better in MC than data)
-d output/ : output directory where the datacard will be written
--xsec $fraction : this is a reference cross-section (xsec) in picobarns (pb). You give whatever you want here but you need to remember this number! Because the datacard is interconnected with this number from now on. Whatever you run in combine with this datacard (limits, best fit r etc) as input will give as output a number. It will not be xsec in pb, but will be a dimensionless number that will have to be multiplied with this reference xsec to be converted to pb. For example if you have set --xsec 0.01 and you run xsec limits and get the number 2.4 as output, that will mean 2.4*0.01pb=0.024pb.
--lumi 137600 : the integrated luminosity of your data
--multi : for use of the envelope method

That will produce 13 different datacards (.txt and .root), one for each alpha bin.

IMPORTANT: This is a simultaneous S+B fit across the 13 alpha bins. This means that you will have to determine ONE xsec value for the signal (lets say 0.01pb), and for each alpha bin you give as --xsec the fraction of this 0.01pb that you expect to have in this particular alpha bin, so that in the end all 13 values add up to 0.01pb. Basically you will have to know how the signal distributes among the 13 alpha bins to see how much each bin contributes to the S+B fit. Simple example: If you expect to have 60% of your signal in alpha bin 8, 20% in alpha bin 7 and 20% in alpha bin 9 then for all other slices you give --xsec 0 , for bins 7 and 9 you give --xsec 0.002 and for bin 8 you give --xsec 0.006.



================================================ Combine datacards ================================================

We want to remove the word "pdf_index" and "discrete" from all datacards except the first one.

For alpha bin = 2 up to 13:

sed -i 's/pdf_index/ /g' dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin"$bin_No".txt
sed -i 's/discrete/ /g'  dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin"$bin_No".txt
sed -i '$ d' dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin"$bin_No".txt


Then combine all 13 datacards into a single one (simultaneous fit):

combineCards.py   dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin1.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin2.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin3.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin4.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin5.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin6.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin7.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin8.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin9.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin10.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin11.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin12.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin13.txt >  card_combined_W-0p0043_8400_2100.txt



================================================ Create workspace from datacard ================================================

Generally, you give the card_combined_W-0p0043_8400_2100.txt as input to run everything, but if you want to submit crab jobs you will need to give the workspace instead. In order to create the workspace do:

text2workspace.py output/datacards_alpha0p25_refxsec0p01pb/card_combined_W-0p0043_8400_2100.txt -o output/datacards_alpha0p25_refxsec0p01pb/workspace_combined_W-0p0043_8400_2100.root



================================================ Asymptotic Limits ================================================

combine -M AsymptoticLimits output/datacards_alpha0p25_refxsec0p01pb/card_combined_W-0p0043_8400_2100.txt --setParameterRanges r=0,5 --saveWorkspace --cminDefaultMinimizerTolerance 0.00100 --cminDefaultMinimizerStrategy 2 --strictBounds -n _W-0p0043_8400_2100

EXPLANATION OF COMMAND:

This prints the observed and the expected limits, along with the +/- 1sigma, 2sigma uncertainties in 95% CL. It also creates a root file with these numbers saved. All the output limit values are dimensionless and should be multiplied with the reference xsec to indicate xsec in pb. 
r = signal strength (xsec of signal). The range of r is important, setting the max value too low or too high can break the fit and yield nonsense results. The max value should be roughly 5 times the +2sigma expected limit. The minimum value should be 0. The r variable in this command is a dimensionless number, rMax = 5 means 5 times the reference xsec with which the datacard is created.
You need to play a bit with rMax to see that you get stable results.



================================================ Asymptotic Significance (observed) ================================================

combine -M Significance output/datacards_alpha0p25_refxsec0p01pb/card_combined_W-0p0043_8400_2100.txt --rMin 0 --rMax 5 --saveWorkspace -n _W-0p0043_8400_2100

EXPLANATION OF COMMAND:

This prints the significance and also creates a root file where the number is saved.
--rMin 0 and --rMax 5 commands are exactly the same as --setParameterRanges r=0,5  : The min, max values should be the same as the ones you have determined for the asymptotic limits, to make sure you get stable results.


================================================ Asymptotic Significance (expected) ================================================

combine -M Significance output/datacards_alpha0p25_refxsec0p01pb/card_combined_W-0p0043_8400_2100.txt -t 200 --expectSignal 1 --rMin 0 --rMax 5 --saveWorkspace --saveToys -n _W-0p0043_8400_2100 --toysFrequentist

EXPLANATION OF COMMAND:

This generates 200 toys in the frequentist way (--toysFrequentist should be added). If -t -1 is given, then an Asimov dataset is generated. Then the toys or Asimov are fitted and the significance for each toy is calculated. 
--rMin 0 and --rMax 5 commands are exactly the same as --setParameterRanges r=0,5  : The min, max values should be the same as the ones you have determined for the asymptotic limits, to make sure you get stable results.
--expectSignal 1 : injects signal equal to the ref xsec. If ref xsec = 0.01 pb then it injects 0.01 pb.

Optional: add --setParameters pdf_index=2 --freezeParameters pdf_index to generate and fit from a specifi function. If this is not specified then by default the toys are generated from the function with pdf_index=0.


================================================ Fit Diagnostics ================================================

combine -M FitDiagnostics output/datacards_alpha0p25_refxsec0p01pb/card_combined_W-0p0043_8400_2100.txt --robustFit 1 --rMin 0 --rMax 5 --setCrossingTolerance 0.00001 --cminDefaultMinimizerStrategy=0 -n _W-0p0043_8400_2100

EXPLANATION OF COMMAND:

This one prints the best fit signal strength (r).
It also creates a root file that contains the post-fit parameters of the background component of the fit (normalization,p1,p2,p3,... parameters) and the post-fit parameters of the signal component of the fit, namely the best fit r, best fit JES, best fit JER etc (the post fit bkg parameters may seem to be the initial ones when printed, this does not affect anything). To view these do the following:

cmsenv
root -l
TFile *f = new TFile("fitDiagnostics_W-0p0043_8400_2100.root");
f->cd();
f->ls();
RooFitResult *s = (RooFitResult*)f->Get("fit_s"); 
fit_s->Print();


================================================ Make DNLL surfaces ================================================

combine -M MultiDimFit output/datacards_alpha0p25_refxsec0p01pb/card_combined_W-0p0043_8400_2100.txt --rMin -0.0008 --rMax 0.7992 --algo grid --points 500 --cminDefaultMinimizerStrategy 0 --saveNLL --X-rtd REMOVE_CONSTANT_ZERO_POINT=1 --robustFit 1 --trackParameters rgx{.*} --saveSpecifiedIndex pdf_index --setParameters pdf_index=0 --freezeParameters pdf_index -n _dijet_W-0p0043_8400_2100

combine -M MultiDimFit output/datacards_alpha0p25_refxsec0p01pb/card_combined_W-0p0043_8400_2100.txt --rMin -0.0008 --rMax 0.7992 --algo grid --points 500 --cminDefaultMinimizerStrategy 0 --saveNLL --X-rtd REMOVE_CONSTANT_ZERO_POINT=1 --robustFit 1 --trackParameters rgx{.*} --saveSpecifiedIndex pdf_index --setParameters pdf_index=1 --freezeParameters pdf_index -n _atlas_W-0p0043_8400_2100

combine -M MultiDimFit output/datacards_alpha0p25_refxsec0p01pb/card_combined_W-0p0043_8400_2100.txt --rMin -0.0008 --rMax 0.7992 --algo grid --points 500 --cminDefaultMinimizerStrategy 0 --saveNLL --X-rtd REMOVE_CONSTANT_ZERO_POINT=1 --robustFit 1 --trackParameters rgx{.*} --saveSpecifiedIndex pdf_index --setParameters pdf_index=2 --freezeParameters pdf_index -n _moddijet_W-0p0043_8400_2100

combine -M MultiDimFit output/datacards_alpha0p25_refxsec0p01pb/card_combined_W-0p0043_8400_2100.txt --rMin -0.0008 --rMax 0.7992 --algo grid --points 500 --cminDefaultMinimizerStrategy 0 --saveNLL --X-rtd REMOVE_CONSTANT_ZERO_POINT=1 --robustFit 1 --trackParameters rgx{.*} --saveSpecifiedIndex pdf_index -n _envelope_W-0p0043_8400_2100

EXPLANATION OF COMMAND:

--rMin -0.0008 --rMax 0.7992 --points 500 : Scan signal strength with 500 points, bin width = (0.7992+0.0008)/500 = 0.0016. The first point that will be scanned is going to be -0.0008+(bin_width/2) = 0.
--setParameters pdf_index=0,1 or 2 --freezeParameters pdf_index : This freezes the pdf index to the dijet, atlas or moddijet function. With these arguments you make the DNLL curve for a specific function. Omit these to create the DNLL for the envelope.


================================================ Plot DNLL surfaces ================================================

root -l
TFile *f = new TFile("higgsCombine_envelope_W-0p0043_8400_2100.MultiDimFit.mH120.root") 
f->cd();
limit->SetMarkerColor(kBlack);
limit->SetMarkerStyle(8);
limit->SetMarkerSize(0.3);
limit->Draw("(2*(deltaNLL+nll0+nll)):r","r>-0.0008 && r<0.7992")

OR/AND:

python3 4jets_scripts/plot1DScan.py higgsCombine_envelope_W-0p0043_2000_500.MultiDimFit.mH120.root -o scan_DNLL_vs_r_vsWidth_8400_2100 --main-color 1 --main-label "#Gamma / M_{S} = 0.43 %" --others higgsCombine_envelope_W-0p015_2000_500.MultiDimFit.mH120.root:"#Gamma / M_{S} = 1.5 %":633 higgsCombine_envelope_W-0p05_2000_500.MultiDimFit.mH120.root:"#Gamma / M_{S} = 5 %":597 higgsCombine_envelope_W-0p1_2000_500.MultiDimFit.mH120.root:"#Gamma / M_{S} = 10 %":418 --y-max 20 --y-cut 20 --Suu 8400 --Chi 2100



================================================ Goodness of fit ================================================

Calculate Λ for data:

combine -M GoodnessOfFit --algo=saturated output/datacards_alpha0p25_refxsec0p01pb/card_combined_W-0p0043_8400_2100.txt -n _W-0p0043_8400_2100_DATA

Add --fixedSignalStrength=0 for the b-only fit.


Calculate Λ for toys:

combine -M GoodnessOfFit --algo=saturated output/datacards_alpha0p25_refxsec0p01pb/card_combined_W-0p0043_8400_2100.txt -t 1000 -s 123456 --toysFrequentist -n _W-0p0043_8400_2100_TOYS

Add --fixedSignalStrength=0 for the b-only fit.

Running toys takes a lot of time so you can submit jobs in crab instead:


cmsenv
voms-proxy-init -voms cms
source /cvmfs/cms.cern.ch/crab3/crab.sh 

combineTool.py -M GoodnessOfFit --algo=saturated -d output/datacards_alpha0p25_refxsec0p01pb/workspace_combined_W-0p0043_8400_2100.root -t 500 -s 123456:123655:1 --toysFrequentist -n _W-0p0043_8400_2100_TOYS --job-mode crab3 --task-name W-0p0043_S-8400_chi-2100 --custom-crab 4jets_scripts/custom_crab.py

Add --fixedSignalStrength=0 for the b-only fit.

EXPLANATION OF COMMAND:

-s 123456:123655:1 means ((123555-123456)+1)*500 = 200*500 = 100000 toys
--custom-crab 4jets_scripts/custom_crab.py : Inside custom_crab.py determine the work area and the output directory

Then, to monitor or resubmit the crab jobs:
crab status output/GOF/crab_W-0p0043_S-8400_chi-2100/
crab resubmit output/GOF/crab_W-0p0043_S-8400_chi-2100/

After all jobs are in finished mode:

crab getoutput output/GOF/crab_W-0p0043_S-8400_chi-2100/
cd output/GOF/crab_W-0p0043_S-8400_chi-2100/results/
for f in *.tar; do tar xf $f; done
hadd higgsCombine_W-0p0043_8400_2100_TOYS.GoodnessOfFit.mH120.root *.root


Now that you have both higgsCombine_W-0p0043_8400_2100_DATA.GoodnessOfFit.mH120.root and higgsCombine_W-0p0043_8400_2100_TOYS.GoodnessOfFit.mH120.root do:

combineTool.py -M CollectGoodnessOfFit --input higgsCombine_W-0p0043_8400_2100_DATA.GoodnessOfFit.mH120.root higgsCombine_W-0p0043_8400_2100_TOYS.GoodnessOfFit.mH120.root -m 120.0 -o gof_W-0p0043_8400_2100.json

python3 4jets_scripts/plotGof.py gof_W-0p0043_8400_2100.json --statistic saturated --mass 120.0 -o gof_W-0p0043_8400_2100 --title-right="Signal plus background fit" --cms-sub="Preliminary" --legend="#splitline{M_{S} = 8400 GeV}{#splitline{M_{#chi} = 2100 GeV}{#Gamma / M_{S} = 0.43 %}}" --x-title="Saturated likelihood ratio" --y-title="Number of toys"




================================================ Generate toy and create TH1D histogram from it ================================================

combine -M GenerateOnly output/datacards_alpha0p25_refxsec0p01pb/dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin8.txt -t 10 --expectSignal 0 --saveToys --toysFrequentist -n _BkgToy_alphabin8 

If -t -1 is given, then an Asimov dataset is generated. The option --toysFrequentist should be added.

TFile *f1 = new TFile("higgsCombine_W-0p0043_8400_2100.AsymptoticLimits.mH120.root")
RooWorkspace* w = (RooWorkspace*)(f1->Get("w"))
RooRealVar* th1x = w->var("th1x")
TFile *f2 = new TFile("higgsCombine_BkgToy_alphabin8.GenerateOnly.mH120.123456.root")
RooDataSet* toy = (RooDataSet*)(f2->Get("toys/toy_1"))
TH1F *h = (TH1F*)toy->createHistogram("th1x")
h->SetName("h_FourjetMass")
TFile *fout = new TFile("HISTOS_4J_Toy_SR_alphabin8.root","RECREATE")
fout->cd()
h->Write()



================================================ Plot significance vs mass ================================================

python3 python/GetCombine_final.py -d output/Wide_Resonances_Run2_Final/combine_rootfiles_alpha0p25/significances/ -m qq --mass range\(2000,11000,100\) --chi range\(500,3000,25\) -b PFDijetRun2 --xsec 0.01 -l 137.6 --toys --asymp_plus_HybridNew 7000 --alpha_true 0.25 --signif --width 0.0043

EXPLANATION OF COMMAND:
--toys is for HybridNew method 
--asymp_plus_HybridNew 7000 means that it takes the HybridNew files instead of the Asymptotic ones for mass>=7000 
If you want to use Asymptotic only significances then do --asymp_plus_HybridNew 12000 (or something larger than your last mass point)

python3 python/Plot1DSignificance_final.py -d output/Wide_Resonances_Run2_Final/combine_rootfiles_alpha0p25/significances/ -m qq -b PFDijetRun2 -l 137.6 --massMin 2000 --massMax 10000  --signif --alpha_true 0.25 --width 0.0043


================================================ Plot limits vs mass ================================================

python3 python/GetCombine_final.py -d output/Wide_Resonances_Run2_Final/combine_rootfiles_alpha0p25/limits/ -m qq --mass range\(2000,11000,100\) --chi range\(500,3000,25\) -b PFDijetRun2 --xsec 0.01 -l 137.6 --toys --asymp_plus_HybridNew 4500 --alpha_true 0.25 --width 0.0043

EXPLANATION OF COMMAND:
--toys is for HybridNew method 
--asymp_plus_HybridNew 4500 means that it takes the HybridNew files instead of the Asymptotic ones for mass>=4500 
If you want to use Asymptotic only limits then do --asymp_plus_HybridNew 12000 (or something larger than your last mass point)

python3 python/Plot1DLimit_final.py -d output/Wide_Resonances_Run2_Final/combine_rootfiles_alpha0p25/limits/ -m qq -b PFDijetRun2 -l 137.6 --massMin 2000 --massMax 9000  --xsecMin 1e-6 --xsecMax 1e-0 --alpha_true 0.25 --width 0.0043
