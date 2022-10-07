This READEME provides instructions on how to run the dijet or paired dijet analysis in CMSSW_12_2_X. We need the DijetTreeMaker (https://github.com/CMSDIJET/DijetRootTreeMaker) that produces the "big trees", where vectors with the jet pt, eta, phi etc are saved, and the DijetTreeAnalyzer (https://github.com/CMSDIJET/DijetRootTreeAnalyzer) that takes as input the "big trees" and creates the "reduced trees" which are smaller trees with variables of interest for the analysis.



%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%% Setup CMSSW and fix scram errors %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

tcsh
cmsrel CMSSW_12_2_1
cd CMSSW_12_2_1/src
cmsenv
git cms-init
git clone https://github.com/CMSDIJET/DijetRootTreeMaker.git CMSDIJET/DijetRootTreeMaker
git clone https://github.com/CMSDIJET/DijetRootTreeAnalyzer.git CMSDIJET/DijetRootTreeAnalyzer
scram b -j 8

There will be errors in CMSSW_12_2_X !

  
For the TreeMaker it pop ups the following error: 

-------------
/eos/home-i/izisopou/4jets_Run3/CMSSW_12_2_1/src/CMSDIJET/DijetRootTreeMaker/plugins/DijetTreeProducer.cc:81:112: error: use of deleted function 'triggerExpression::Data& triggerExpression::Data::operator=(triggerExpression::Data&&)'
   81 |   triggerCache_       = triggerExpression::Data(cfg.getParameterSet("triggerConfiguration"),consumesCollector());
      |                                                                                                                ^
In file included from /eos/home-i/izisopou/4jets_Run3/CMSSW_12_2_1/src/CMSDIJET/DijetRootTreeMaker/plugins/DijetTreeProducer.h:9,
                 from /eos/home-i/izisopou/4jets_Run3/CMSSW_12_2_1/src/CMSDIJET/DijetRootTreeMaker/plugins/DijetTreeProducer.cc:16:
/cvmfs/cms.cern.ch/slc7_amd64_gcc900/cms/cmssw/CMSSW_12_2_1/src/HLTrigger/HLTcore/interface/TriggerExpressionData.h:23:9: note: 'triggerExpression::Data& triggerExpression::Data::operator=(triggerExpression::Data&&)' is implicitly deleted because the default definition would be ill-formed:
   23 |   class Data {
      |         ^~~~
-------------


To solve this, in the code CMSDIJET/DijetRootTreeMaker/plugins/DijetTreeProducer.cc comment out the following lines:
triggerCache_       = triggerExpression::Data(cfg.getParameterSet("triggerConfiguration"),consumesCollector());
noiseFilterCache_   = triggerExpression::Data(cfg.getParameterSet("noiseFilterConfiguration"),consumesCollector());

and in the beginning, instead of 

DijetTreeProducer::DijetTreeProducer(edm::ParameterSet const& cfg)
{

write:

DijetTreeProducer::DijetTreeProducer(edm::ParameterSet const& cfg)
: triggerCache_(cfg.getParameterSet("triggerConfiguration"),consumesCollector()),
noiseFilterCache_(cfg.getParameterSet("noiseFilterConfiguration"),consumesCollector())
{ 

Afterwards, doing scram b -j 8 will show errors concerning some python scripts:

In CMSSW_12_2_X there was the transition of python2 to python3, so we should also fix some of the python scripts for which python3 gives compilation errors. This includes changing tabs to spaces, putting parentheses in print and exec statements and making the following change:

for k, g in groupby(enumerate(sidebandBins), lambda (i,x):i-x):  --->  for k, g in groupby(enumerate(sidebandBins), lambda i,x:i-x):

Instead of fixing them one by one you can get the updated python scripts, for which there are errors, from here:

/afs/cern.ch/work/n/nsaoulid/public/CMSSW_12_2_1/src/CMSDIJET/DijetRootTreeAnalyzer/


Finally, do scram b -j 8 again and there should be no errors.




%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%% How to find the dataset in DAS %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

First we search for the dataset in DAS: https://cmsweb.cern.ch/das/

e.g. we type in the search bar: dataset status=* dataset=/QCD_Pt-15to7000*13p6*/*Run3Winter22*/MINIAODSIM

In each dataset we want to process we click the "Release" and the "Config" buttons to see the CMSSW release and the Global Tag (GT). E.g. for one of the above datasets: we get:

CMSSW_12_2_1 -> Release
122X_mcRun3_2021_realistic_v9 -> Global Tag

In order to see which jet collections are stored in a MINIAOD dataset:
1) Find the dataset in DAS, click "Files" and choose one of the root files
2) Under a CMSSW release in your lxplus do:

cmsenv
voms-proxy-init -voms cms
edmDumpEventContent root://cms-xrd-global.cern.ch//store/blabla

where /store/blabla is the root file from DAS

This will print the jet collections. For example in MINIAOD currently:

slimmedJets 		-> AK4CHS jets
slimmedJetsPuppi 	-> AK4PUPPI jets
slimmedJetsAK8 	-> AK8PUPPI jets

slimmedGenJets 	-> gen AK4 jets
slimmedGenJetsAK8	-> gen AK8 jets


%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%% How to run Big Trees (Local test + submission on CRAB) %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

Work directory: $CMSSW_BASE/src/CMSDIJET/DijetRootTreeMaker/


Take the most recent DijetTreeMaker from here:

cp /afs/cern.ch/user/i/izisopou/public/ForAaron/DijetAnalysisSetupCodes/DijetTreeProducer.cc $CMSSW_BASE/src/CMSDIJET/DijetRootTreeMaker/plugins/
cp /afs/cern.ch/user/i/izisopou/public/ForAaron/DijetAnalysisSetupCodes/DijetTreeProducer.h $CMSSW_BASE/src/CMSDIJET/DijetRootTreeMaker/plugins/

This one saves variables for two jet collections in the same tree, one with name "AK4" and one with the name "AK8". What kind of jet collections you will save is given by the py scripts below.

Take also the most recent py scripts to run the above code:

cp /afs/cern.ch/user/i/izisopou/public/ForAaron/DijetAnalysisSetupCodes/flat-MC-cfg_miniAOD_AK4CHSandAK4PUPPI.py $CMSSW_BASE/src/CMSDIJET/DijetRootTreeMaker/prod/
cp /afs/cern.ch/user/i/izisopou/public/ForAaron/DijetAnalysisSetupCodes/flat-data-cfg_miniAOD_AK4CHSandAK4PUPPI.py $CMSSW_BASE/src/CMSDIJET/DijetRootTreeMaker/prod/

The above py scripts save AK4 CHS jets with the name "AK4" in the tree variables and AK4 PUPPI jets with the name "AK8" in the tree variables. The first py script is to run a MC sample and the second one to run data.


Every time you log in to lxplus:

cd $CMSSW_BASE/src/
cmsenv
voms-proxy-init -voms cms


!!!! Before submitting to crab -> LOCAL RUN - CMS RUN !!!!

How to locally run a test in order to see if the code works as you wish before you send it to crab. (Example of running MC below)


Go to the flat-MC-cfg_miniAOD_AK4CHSandAK4PUPPI.py inside $CMSSW_BASE/src/CMSDIJET/DijetRootTreeMaker/prod/ : 

1) The file that will run localy is defined in L84. Go to the dataset in DAS and click "Files", choose one of the root files and put the root://cms-xrd-global.cern.ch/ in front

2) Find the Global Tag of the dataset and put it in L14. Comment out L13.

3) Uncomment out L27:  fileName=cms.string('test.root') and comment out L26.

4) Go to L19 and choose how many events to process as a test (for example 100 events)

Run the code by executing:

cmsRun flat-MC-cfg_miniAOD_AK4CHSandAK4PUPPI.py


Remember to change back the lines for 2) and 3) in order to run on CRAB, i.e. to run in CRAB you need to comment out L14 and L27 and keep in the code the L13 and L26.

Similarly you can do a local test with flat-data-cfg_miniAOD_AK4CHSandAK4PUPPI.py

If the test.root is produced properly (with the amount of events you selected and all variables filled correctly) then everything is okay and you can proceed with the CRAB submission which is explained below for MC and data:


I) RUN DATA ON CRAB

cd $CMSSW_BASE/src/CMSDIJET/DijetRootTreeMaker/prod/submitJobsWithCrab3/

The command to submit jobs to crab is the following:

=======================================================================
python createAndSubmitDATA.py -d Output -v 2018_data -i Inputs/InputList.txt  -t crab3_template_data.py -c ../flat-data-cfg_miniAOD_AK4CHSandAK4PUPPI.py -n $USER --submit
========================================================================

Explanation of the above command and what you need to do before you execute it:

Inside $CMSSW_BASE/src/CMSDIJET/DijetRootTreeMaker/prod/submitJobsWithCrab3/ you create beforehand the folder: Output/

Once you submit the jobs, the crab task will be in a directory : /Output/2018_data_20220113_1905/ (-v 2018_data creates automatically the folder 2018_data_20220113_1905 where the last part is the date and time you submitted the task)

You create beforehand a folder: Inputs/ and inside it a .txt InputList.txt -> In this txt you need to write something like the following line:

/MinimumBias/Run2018C-PromptReco-v3/MINIAOD -1 1  101X_dataRun2_Prompt_v11

This corresponds to:  Name of dataset in DAS, how many events to process (-1 to run all events), how many files per job, Global Tag 

If you want to process more than one datasets, you can have multiple of those lines.

In the script crab3_template_data.py :

1)Put the correct JSON file ( For minimum Bias this step is commented out) -> Find the JSON file that correspond to data

 config.Data.lumiMask = '/afs/cern.ch/work/m/mdiamant/private/CMSSW_9_2_13_TreeMaker/src/CMSDIJET/DijetRootTreeMaker/prod/submitJobsWithCrab3/JSON_Certified/Cert_294927-305636_13TeV_PromptReco_Collisions17_JSON.txt' (you do not usually run min bias with a JSON file)
                      
2) Put the output directory in EOS to save the big trees
 
config.Data.outLFNDirBase = '/store/group/phys_jetmet/magda/bigtrees_test/'



II) RUN MC ON CRAB

Same logic as for data. To submit jobs do:

=======================================================
python createAndSubmitMC.py -d Output/ -v Diquark_signals -i Inputs/InputList.txt -t crab3_template_MC.py -c ../flat-MC-cfg_miniAOD_AK4CHSandAK4PUPPI.py -n $USER --submit
=======================================================

We do not have a JSON file here in MC, so in the crab3_template_MC.py just change the output directory.



Once you have submitted the jobs check their status:

crab status Output/Diquark_signals_YYYYMMDD_HHMM/

Resubmit jobs if a few have failed due to server errors etc:

crab resubmit Output/Diquark_signals_YYYYMMDD_HHMM/


Once all jobs are in finished mode, all big trees will have appeared in the output file you have specified in EOS.


For DATA only: How to get the JSON from proccessed big trees

1) Open the "crab.log" file of the processed job

2) Search "Task name" and copy the part: 181113_164416:dkarasav_crab_JetHT__Run2018A-17Sep2018-v1__MINIAOD

3) Go to the directory that you gave on the on the "-d" argument (e.g. -d Output_JetHT_rereco_2018/ ) 

4) Do the command: crab remake --task=181113_164416:dkarasav_crab_JetHT__Run2018A-17Sep2018-v1__MINIAOD

5) Then a directory is created with name "crab_JetHT__Run2018A-17Sep2018-v1__MINIAOD/ ", so do the command: 

6) crab report crab_JetHT__Run2018A-17Sep2018-v1__MINIAOD

7) Inside it, in the directory "results" you find the JSON file with name: processedLumis.json

8) Rename the json file to have the name of the dataset



%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%% How to run Reduced Trees %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

Work directory: $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/

cd $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/

----------------------------------------
For the Dijet analysis take the following files:

cp /afs/cern.ch/user/i/izisopou/public/ForAaron/DijetAnalysisSetupCodes/analysisClass_mainDijetSelection_trigger_JetHTDataset_v2_cemf_lt_0p8_DIJET.C $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/src/

cp -rf /afs/cern.ch/user/i/izisopou/public/ForAaron/DijetAnalysisSetupCodes/cutFile_DIJET/ $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/config/

cp /afs/cern.ch/user/i/izisopou/public/ForAaron/DijetAnalysisSetupCodes/analysisClass.h $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/include/

cp /afs/cern.ch/user/i/izisopou/public/ForAaron/DijetAnalysisSetupCodes/Input_list.txt $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/lists/  ---> This is just an example to see what the format of the txt should be. In this file we give the location of all big trees that we want to analyze. (CAREFUL: Make sure there is no empty line after the last entry in the text file.)

----------------------------------------


----------------------------------------
For the Paired dijet analysis take the following files:

cp /afs/cern.ch/user/i/izisopou/public/ForAaron/DijetAnalysisSetupCodes/analysisClass_mainDijetSelection_4Jets_signal_minDeltaR.C $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/src/

cp /afs/cern.ch/user/i/izisopou/public/ForAaron/DijetAnalysisSetupCodes/cutFile_mainDijetSelection_JetHT_4jets.txt $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/config/

cp /afs/cern.ch/user/i/izisopou/public/ForAaron/DijetAnalysisSetupCodes/analysisClass.h $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/include/

cp /afs/cern.ch/user/i/izisopou/public/ForAaron/DijetAnalysisSetupCodes/Input_list.txt $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/lists/  ---> This is just an example to see what the format of the txt should be. In this file we give the location of all big trees that we want to analyze. (CAREFUL: Make sure there is no empty line after the last entry in the text file.)

----------------------------------------

In the analyzer (the .C file) we fill a variable in the tree by doing fillVariableWithValue(...)


Now that you have all files you need:


./scripts/make_rootNtupleClass.sh -f /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/magda/4Jets_Suu_Diquark_2017/Suu_Diquark_S8000_chi2000_TuneCP2_13TeV-madgraph-pythia8/crab_Suu_Diquark_S8000_chi2000_TuneCP2_13TeV-madgraph-pythia8/200831_074002/0000/Suu_Diquark_S8000_chi2000_TuneCP2_13TeV-madgraph-pythia8__RunIIFall17MiniAODv2-PU2017_12Apr2018_94X_mc2017_realistic_v14-v1__MINIAODSIM_1.root -t dijets/events

answer yes in the two questions.

This step will create the file rootNtupleClass.h in the include/ folder

( /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/magda/4Jets_Suu_Diquark_2017/Suu_Diquark_S8000_chi2000_TuneCP2_13TeV-madgraph-pythia8/crab_Suu_Diquark_S8000_chi2000_TuneCP2_13TeV-madgraph-pythia8/200831_074002/0000/Suu_Diquark_S8000_chi2000_TuneCP2_13TeV-madgraph-pythia8__RunIIFall17MiniAODv2-PU2017_12Apr2018_94X_mc2017_realistic_v14-v1__MINIAODSIM_1.root is one of the big trees you produced earlier, only one of the big trees is enough, this step is to save what kind of variables are saved in the big tree)


ln -sf analysisClass_mainDijetSelection_trigger_JetHTDataset_v2_cemf_lt_0p8_DIJET.C src/analysisClass.C
So when you do
ls -l src/analysisClass.C
it should write
lrwxr-xr-x. 1 etzia zh 57 Νο�  6 11:24 src/analysisClass.C -> analysisClass_mainDijetSelection_trigger_JetHTDataset_v2_cemf_lt_0p8_DIJET.C

make clean
make

(whenever you change the a .C (or .h) file you should always do "make clean", "make")

No error message should appeared, but in the first time you will come across some errors. To solve them:


1) In the include/baseClass.h:

add the

#include <TObjString.h>

2) Take this file instead: cp /afs/cern.ch/work/n/nsaoulid/public/ForIlias/Makefile $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/

3) If you are getting an error about BTagCalibration while compiling with "make" comment out in the analyzer (.C script) the lines:

//load btag scale factors
  /*bcalib = new BTagCalibration("CSVv2", "data/bTag_MC_ScalingFactors/CSVv2_ichep.csv");

  //medium WP
  breader_medium = new BTagCalibrationReader(BTagEntry::OP_MEDIUM,  // operating point
                         "central",             // central sys type
                         {"up", "down"});      // other sys types
  breader_medium->load(*bcalib,                // calibration instance
               BTagEntry::FLAV_B,    // btag flavour
               "comb");
  //tight WP
  breader_tight = new BTagCalibrationReader(BTagEntry::OP_TIGHT,  // operating point
                        "central",             // central sys type
                        {"up", "down"});      // other sys types
  breader_tight->load(*bcalib,                // calibration instance
              BTagEntry::FLAV_B,    // btag flavour
              "comb");
  */

After the above, executing "make" should give no errors.

HOW TO RUN and produce the reduced tree:

./main lists/Input_list.txt config/cutFile_DIJET/cutFile_mainDijetSelection_JetHT_Run2017B-17Nov2017-v1.txt dijets/events output/test output/test

If everything goes well then in the output folder the file test_reduced_skim.root will appear, which is the reduced tree. Inside this tree do rootTupleTree->cd() and in there there will be the tree filled with the variables of interest.





                




