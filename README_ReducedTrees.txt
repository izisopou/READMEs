============Install CMS release==================

tcsh
setenv SCRAM_ARCH slc7_amd64_gcc700
scram p -n CMSSW_10_5_0 CMSSW_10_5_0 
cd CMSSW_10_5_0/src
git clone https://github.com/CMSDIJET/DijetRootTreeAnalyzer.git CMSDIJET/DijetRootTreeAnalyzer
cmsenv
cd CMSDIJET/DijetRootTreeAnalyzer/
./scripts/make_rootNtupleClass.sh -f /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/magda/4Jets_Suu_Diquark_2017/Suu_Diquark_S8000_chi2000_TuneCP2_13TeV-madgraph-pythia8/crab_Suu_Diquark_S8000_chi2000_TuneCP2_13TeV-madgraph-pythia8/200831_074002/0000/Suu_Diquark_S8000_chi2000_TuneCP2_13TeV-madgraph-pythia8__RunIIFall17MiniAODv2-PU2017_12Apr2018_94X_mc2017_realistic_v14-v1__MINIAODSIM_1.root -t dijets/events

answer yes in the two questions.

This step will create the file rootNtupleClass.h in the /include folder
(The *.root has to be produced with the same release, i.e. CMSSW_10_5_0).


==========Analyzer & Config =================

-Copy the /afs/cern.ch/user/e/etzia/public/forMaria/4jets
analysisClass_mainDijetSelection_4Jets_signal_minDeltaR.C    in the /src/ inside the DijetRootTreeAnalyzer
cutFile_mainDijetSelection_JetHT_4jets_2017B.txt in the /config/ inside the DijetRootTreeAnalyzer
analysisClass.h   in the /include/ inside the DijetRootTreeAnalyzer
the folder MC2017 in the /lists/ inside the DijetRootTreeAnalyzer <- it has the lists with the input files for all the signal samples

========= Compile ================
ln -sf analysisClass_mainDijetSelection_4Jets_signal_minDeltaR.C src/analysisClass.C
So when you do
ls -l src/analysisClass.C
it should write
lrwxr-xr-x. 1 etzia zh 57 Νο�  6 11:24 src/analysisClass.C -> analysisClass_mainDijetSelection_4Jets_signal_minDeltaR.C

make clean
make

(whenever you change the a .C (or .h) file you should always do "make clean", "make")
No error message should appeared.

============HOW TO RUN=======================

./main lists/MC2017/InputList_Signal_Suu_Diquark_S2000_chi500.txt config/cutFile_mainDijetSelection_JetHT_4jets_2017B.txt dijets/events output/Signal_Suu_Diquark_S2000_chi500 output/test
if it runs fine in the output folder the file Signal_Suu_Diquark_S2000_chi500_reduced_skim.root will appear.

Always do "cmsenv" in the folder CMSSW_10_5_0/src/CMSDIJET/DijetRootTreeAnalyzer/ whenever you start to work 
==================================================

The input files are called big trees and the output reduced trees.
If you do
root /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/magda/4Jets_Suu_Diquark_2017/Suu_Diquark_S8000_chi2000_TuneCP2_13TeV-madgraph-pythia8/crab_Suu_Diquark_S8000_chi2000_TuneCP2_13TeV-madgraph-pythia8/200831_074002/0000/Suu_Diquark_S8000_chi2000_TuneCP2_13TeV-madgraph-pythia8__RunIIFall17MiniAODv2-PU2017_12Apr2018_94X_mc2017_realistic_v14-v1__MINIAODSIM_1.root
(and now inside root)
.ls
dijets->cd()
.ls
events->Print()

all the variables that are inside the big trees will be appeared.
The "true" or "generated" variables are gen_*, such as gen_pt.

no_genjets_AK4 = jetPtGenAK4->size();
for(Int_tgi=0;i<no_genjets_AK4;i++){
    pTGen=jetPtGenAK4->at(i);                
                                        
} 
                
In the analyzer (the .C file) we fill a variable in the tree by doing fillVariableWithValue(...)





