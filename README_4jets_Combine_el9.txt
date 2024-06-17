
********** Setup CMSSW and codes **********

Login to lxplus9 and create a folder. Inside it:

cmsrel CMSSW_14_1_0_pre4
cd CMSSW_14_1_0_pre4/src
cmsenv
git clone -b PairedDijetAnalysis https://github.com/CMSDIJET/DijetRootTreeAnalyzer CMSDIJET/DijetRootTreeAnalyzer
git clone https://github.com/cms-analysis/HiggsAnalysis-CombinedLimit.git HiggsAnalysis/CombinedLimit   
git clone https://github.com/cms-analysis/CombineHarvester.git CombineHarvester

scram b CombineHarvester

cd HiggsAnalysis/CombinedLimit
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


From /afs/cern.ch/user/i/izisopou/public/Combine_Codes take BinnedFit_4jets.py, WriteDataCard_4jets.py, WriteDataCard_4jets_envelope.py and copy them inside $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/python

From /afs/cern.ch/user/i/izisopou/public/Combine_Codes take plot1DScan.py, custom_crab_GOF.py and copy them inside $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer




From now on, main work directory is the $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer





********** Background only fits with the 3 different functions **********

For alpha bin = 1 up to 13:

python python/BinnedFit_4jets.py -c config/Configfiles_13_slices_dijet_only/run2_fourjet_alpha"$bin_No"_dijet_only_upto10072.config -l 137600 /eos/user/i/izisopou/4jets_fits_run2andrun3/CMSSW_10_2_13/src/CMSDIJET/DijetRootTreeAnalyzer/inputs/run2/data/HISTOS_4J_Alldata_SR_alphabin"$bin_No".root -b PFDijetRun2_4J_alphabin"$bin_No" -d output/Wide_Resonances_RunII_Initial_Request/BkgOnlyFits/Dijet-3p/ --fit-spectrum

python python/BinnedFit_4jets.py -c config/Configfiles_13_slices_atlas_only/run2_fourjet_alpha"$bin_No"_atlas_only_upto10072.config -l 137600 /eos/user/i/izisopou/4jets_fits_run2andrun3/CMSSW_10_2_13/src/CMSDIJET/DijetRootTreeAnalyzer/inputs/run2/data/HISTOS_4J_Alldata_SR_alphabin"$bin_No".root -b PFDijetRun2_4J_alphabin"$bin_No" -d output/Wide_Resonances_RunII_Initial_Request/BkgOnlyFits/Atlas-3p/ --fit-spectrum

python python/BinnedFit_4jets.py -c config/Configfiles_13_slices_moddijet_only/run2_fourjet_alpha"$bin_No"_moddijet_only_upto10072.config -l 137600 /eos/user/i/izisopou/4jets_fits_run2andrun3/CMSSW_10_2_13/src/CMSDIJET/DijetRootTreeAnalyzer/inputs/run2/data/HISTOS_4J_Alldata_SR_alphabin"$bin_No".root -b PFDijetRun2_4J_alphabin"$bin_No" -d output/Wide_Resonances_RunII_Initial_Request/BkgOnlyFits/Moddijet-3p/ --fit-spectrum



********** Create datacard **********


For alpha bin = 1 up to 13:

python python/WriteDataCard_4jets_envelope.py -b PFDijetRun2_4J_alphabin"$bin_No" -c config/Configfiles_13_slices_Envelope_3_functions/run2_fourjet_alpha"$bin_No"_multipdf_3_func_upto10072.config -m W-0p0043_Suu --model2 Chi --mass 8400 --mass2 2100 /eos/user/i/izisopou/4jets_fits_run2andrun3/CMSSW_10_2_13/src/CMSDIJET/DijetRootTreeAnalyzer/inputs/run2/data/HISTOS_4J_Alldata_SR_alphabin"$bin_No".root /eos/user/i/izisopou/4jets_fits_run2andrun3/CMSSW_10_2_13/src/CMSDIJET/DijetRootTreeAnalyzer/inputs/run2/signal/ResonanceShapes_NOMINAL_Suu-Diquark_W-0p0043_S-8400_chi-2100_alphabin"$bin_No".root --jesUp /eos/user/i/izisopou/4jets_fits_run2andrun3/CMSSW_10_2_13/src/CMSDIJET/DijetRootTreeAnalyzer/inputs/run2/signal/ResonanceShapes_NOMINAL_Suu-Diquark_W-0p0043_S-8400_chi-2100_alphabin"$bin_No".root --jesDown /eos/user/i/izisopou/4jets_fits_run2andrun3/CMSSW_10_2_13/src/CMSDIJET/DijetRootTreeAnalyzer/inputs/run2/signal/ResonanceShapes_NOMINAL_Suu-Diquark_W-0p0043_S-8400_chi-2100_alphabin"$bin_No".root --jerUp /eos/user/i/izisopou/4jets_fits_run2andrun3/CMSSW_10_2_13/src/CMSDIJET/DijetRootTreeAnalyzer/inputs/run2/signal/ResonanceShapes_NOMINAL_Suu-Diquark_W-0p0043_S-8400_chi-2100_alphabin"$bin_No".root --jerDown /eos/user/i/izisopou/4jets_fits_run2andrun3/CMSSW_10_2_13/src/CMSDIJET/DijetRootTreeAnalyzer/inputs/run2/signal/ResonanceShapes_NOMINAL_Suu-Diquark_W-0p0043_S-8400_chi-2100_alphabin"$bin_No".root -d output/Wide_Resonances_RunII_Initial_Request/datacards_alpha0p25_refxsec0p0001pb/ --xsec $fraction --lumi 137600 --multi



********** Remove "pdf index" from datacards and combine them **********

For alpha bin = 2 up to 13:

sed -i 's/pdf_index/ /g' dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin"$bin_No".txt
sed -i 's/discrete/ /g'  dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin"$bin_No".txt
sed -i '$ d' dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin"$bin_No".txt


Then combine:

   combineCards.py   dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin1.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin2.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin3.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin4.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin5.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin6.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin7.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin8.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin9.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin10.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin11.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin12.txt dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin13.txt >  card_combined_W-0p0043_8400_2100.txt



********** Create workspace from datacard (need to give that as input when submitting jobs to crab) **********

text2workspace.py output/Wide_Resonances_RunII_Initial_Request/datacards_alpha0p25_refxsec0p0001pb/card_combined_W-0p0043_8400_2100.txt -o output/Wide_Resonances_RunII_Initial_Request/datacards_alpha0p25_refxsec0p0001pb/workspace_combined_W-0p0043_8400_2100.root



********** Asymptotic Limits **********

combine -M AsymptoticLimits output/Wide_Resonances_RunII_Initial_Request/datacards_alpha0p25_refxsec0p0001pb/card_combined_W-0p0043_8400_2100.txt  --setParameterRanges r=0,5 --saveWorkspace --cminDefaultMinimizerTolerance 0.00100 --cminDefaultMinimizerStrategy 2 --strictBounds -n _W-0p0043_8400_2100



********** Asymptotic Significance (observed) **********

combine -M Significance output/Wide_Resonances_RunII_Initial_Request/datacards_alpha0p25_refxsec0p0001pb/card_combined_W-0p0043_8400_2100.txt --rMin 0 --rMax 3 --saveWorkspace -n _W-0p0043_8400_2100



********** Fit Diagnostics **********

combine -M FitDiagnostics output/Wide_Resonances_RunII_Initial_Request/datacards_alpha0p25_refxsec0p0001pb/card_combined_W-0p0043_8400_2100.txt --robustFit 1 --rMin 0 --rMax 3 --setCrossingTolerance 0.00001 --cminDefaultMinimizerStrategy=0 -n _W-0p0043_8400_2100



********** Make DNLL surfaces **********

combine -M MultiDimFit output/Wide_Resonances_RunII_Initial_Request/datacards_alpha0p25_refxsec0p0001pb/card_combined_W-0p0043_8400_2100.txt --rMin -0.0008 --rMax 0.7992 --algo grid --points 500 --cminDefaultMinimizerStrategy 0 --saveNLL --X-rtd REMOVE_CONSTANT_ZERO_POINT=1 --robustFit 1 --trackParameters rgx{.*} --saveSpecifiedIndex pdf_index --setParameters pdf_index=0 --freezeParameters pdf_index -n _W-0p0043_8400_2100_Dijet

combine -M MultiDimFit output/Wide_Resonances_RunII_Initial_Request/datacards_alpha0p25_refxsec0p0001pb/card_combined_W-0p0043_8400_2100.txt --rMin -0.0008 --rMax 0.7992 --algo grid --points 500 --cminDefaultMinimizerStrategy 0 --saveNLL --X-rtd REMOVE_CONSTANT_ZERO_POINT=1 --robustFit 1 --trackParameters rgx{.*} --saveSpecifiedIndex pdf_index --setParameters pdf_index=1 --freezeParameters pdf_index -n _W-0p0043_8400_2100_Atlas

combine -M MultiDimFit output/Wide_Resonances_RunII_Initial_Request/datacards_alpha0p25_refxsec0p0001pb/card_combined_W-0p0043_8400_2100.txt --rMin -0.0008 --rMax 0.7992 --algo grid --points 500 --cminDefaultMinimizerStrategy 0 --saveNLL --X-rtd REMOVE_CONSTANT_ZERO_POINT=1 --robustFit 1 --trackParameters rgx{.*} --saveSpecifiedIndex pdf_index --setParameters pdf_index=2 --freezeParameters pdf_index -n _W-0p0043_8400_2100_Moddijet

combine -M MultiDimFit output/Wide_Resonances_RunII_Initial_Request/datacards_alpha0p25_refxsec0p0001pb/card_combined_W-0p0043_8400_2100.txt --rMin -0.0008 --rMax 0.7992 --algo grid --points 500 --cminDefaultMinimizerStrategy 0 --saveNLL --X-rtd REMOVE_CONSTANT_ZERO_POINT=1 --robustFit 1 --trackParameters rgx{.*} --saveSpecifiedIndex pdf_index -n _W-0p0043_8400_2100_Envelope



********** Plot DNLL surfaces **********

python plot1DScan.py output/Wide_Resonances_RunII_Initial_Request/combine_rootfiles/MultiDimFit/higgsCombine_W-0p0043_8400_2100_Envelope.MultiDimFit.mH120.root -o scan_DNLL_vs_r_diffWidths_8400_2100 --main-color 1 --main-label "#Gamma/M = 0.43 %" --others output/Wide_Resonances_RunII_Initial_Request/combine_rootfiles/MultiDimFit/higgsCombine_W-0p015_8400_2100_Envelope.MultiDimFit.mH120.root:"#Gamma/M = 1.5 %":633 output/Wide_Resonances_RunII_Initial_Request/combine_rootfiles/MultiDimFit/higgsCombine_W-0p05_8400_2100_Envelope.MultiDimFit.mH120.root:"#Gamma/M = 5 %":597 output/Wide_Resonances_RunII_Initial_Request/combine_rootfiles/MultiDimFit/higgsCombine_W-0p07_8400_2100_Envelope.MultiDimFit.mH120.root:"#Gamma/M = 7 %":801 output/Wide_Resonances_RunII_Initial_Request/combine_rootfiles/MultiDimFit/higgsCombine_W-0p1_8400_2100_Envelope.MultiDimFit.mH120.root:"#Gamma/M = 10 %":880 --y-max 15 --y-cut 15 --logo-sub "Preliminary"



********** Goodness of fit **********

combine -M GoodnessOfFit --algo=saturated output/Wide_Resonances_RunII_Initial_Request/datacards_alpha0p25_refxsec0p0001pb/card_combined_W-0p0043_8400_2100.txt -n _W-0p0043_8400_2100_DATA


For toy generation:

combine -M GoodnessOfFit --algo=saturated output/Wide_Resonances_RunII_Initial_Request/datacards_alpha0p25_refxsec0p0001pb/card_combined_W-0p0043_8400_2100.txt -t 1000 -s 123456 --toysFrequentist -n _W-0p0043_8400_2100_TOYS


OR you can submit jobs in crab if you want to generate many toys:


cmsenv
voms-proxy-init -voms cms
source /cvmfs/cms.cern.ch/common/crab-setup.sh

Create workspace from the datacard:

text2workspace.py output/Wide_Resonances_RunII_Initial_Request/datacards_alpha0p25_refxsec0p0001pb/card_combined_W-0p0043_8400_2100.txt -o output/Wide_Resonances_RunII_Initial_Request/datacards_alpha0p25_refxsec0p0001pb/workspace_combined_W-0p0043_8400_2100.root


combineTool.py -M GoodnessOfFit --algo=saturated -d output/Wide_Resonances_RunII_Initial_Request/datacards_alpha0p25_refxsec0p0001pb/workspace_combined_W-0p0043_8400_2100.root -t 500 -s 123456:123655:1 --toysFrequentist -n _W-0p0043_8400_2100_TOYS --job-mode crab3 --task-name W-0p0043_S-8400_chi-2100 --custom-crab custom_crab_GOF.py

-s 123456:123655:1 means ((123555-123456)+1)*500 = 200*500 = 100000 toys


crab getoutput output/Wide_Resonances_RunII_Initial_Request/GOF/crab_W-0p0043_S-8400_chi-2100/

cd output/Wide_Resonances_RunII_Initial_Request/GOF/crab_W-0p0043_S-8400_chi-2100/results/

for f in *.tar; do tar xf $f; done

hadd all root files to produce higgsCombine_W-0p0043_8400_2100_TOYS.GoodnessOfFit.mH120.root


combineTool.py -M CollectGoodnessOfFit --input higgsCombine_W-0p0043_8400_2100_DATA.GoodnessOfFit.mH120.root higgsCombine_W-0p0043_8400_2100_TOYS.GoodnessOfFit.mH120.root -m 120.0 -o gof_W-0p0043_8400_2100.json

plotGof.py gof_W-0p0043_8400_2100.json --statistic saturated --mass 120.0 -o gof_W-0p0043_8400_2100 --title-right="M(S) = 8.4 TeV, M(#chi) = 2.1 TeV, Width = 0.43 %"

OR

local script:

python plotGof.py gof_CMSplusATLASpseudodataset_W-0p015_9000_2250.json --statistic saturated --mass 120.0 -o gof_CMSplusATLASpseudodataset_W-0p015_9000_2250 --title-right="M(S) = 9.0 TeV, M(#chi) = 2.25 TeV, Width = 1.5 %" --cms-sub="Preliminary" --legend="CMS + ATLAS pseudo-dataset" --x-title="Goodness of fit" --y-title="Number of toys"



Add --fixedSignalStrength=0 for GOF for b-only fit.




********** Generate toy and create TH1D histogram from it **********

combine output/Wide_Resonances_RunII_Initial_Request/datacards_alpha0p25_refxsec0p0001pb/dijet_combine_W-0p0043_Suu_Chi_8400_2100_lumi-137.600_PFDijetRun2_4J_alphabin1.txt -M GenerateOnly -t 1 --expectSignal 0 --saveToys --toysFrequentist -n _BkgToy_alphabin1 


TFile *f1 = new TFile("higgsCombine_W-0p0043_8400_2100.AsymptoticLimits.mH120.root")
RooWorkspace* w = (RooWorkspace*)(f1->Get("w"))
RooRealVar* th1x = w->var("th1x")
TFile *f2 = new TFile("higgsCombine_BkgToy_alphabin13.GenerateOnly.mH120.123456.root")
RooDataSet* toy = (RooDataSet*)(f2->Get("toys/toy_2"))
TH1F *h = (TH1F*)toy->createHistogram("th1x")
h->SetName("h_FourjetMass")
TFile *fout = new TFile("inputs/run2/ToysFromData/HISTOS_4J_Toy_SR_alphabin13.root","RECREATE")
fout->cd()
h->Write()



********** Plot significance vs mass **********

python3 python/GetCombine_with_HybridNew_final.py -d output/Wide_Resonances_Run2_Final/combine_rootfiles_alpha0p25/significances/ -m qq --mass range\(2000,11000,100\) --chi range\(500,3000,25\) -b PFDijetRun2 --xsec 0.0001 -l 137.6 --toys --asymp_plus_HybridNew 11000 --alpha_true 0.25 --signif --width 0.015

Explanation: --toys is for HybridNew method, --asymp_plus_HybridNew 11000 means that it takes the HybridNew files instead of the Asymptotic ones for mass>11000 (here we have put 11000 so that all mass points are Asymptotic)

python3 python/Plot1DSignificance_final.py -d output/Wide_Resonances_Run2_Final/combine_rootfiles_alpha0p25/significances/ -m qq -b PFDijetRun2 -l 137.6 --massMin 2000 --massMax 10000  --signif --alpha_true 0.25 --width 0.015
