
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%% BIG TREES/NTUPLES %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%



=========== Setup CMSSW ===========

cmsrel CMSSW_13_0_3
cd CMSSW_13_0_3/src
cmsenv
git clone -b Run3_130X https://github.com/CMSDIJET/DijetRootTreeMaker.git CMSDIJET/DijetRootTreeMaker
scram b -j 8


There should be no errors.



=========== How to find the dataset in DAS ===========

First we search for the dataset in DAS: https://cmsweb.cern.ch/das/

e.g. we type in the search bar: dataset status=* dataset=/QCD_Pt-15to7000*13p6*/*Run3Winter22*/MINIAODSIM

In each dataset we want to process we click the "Release" and the "Config" buttons to see the CMSSW release and the Global Tag (GT). E.g. for one of the above datasets: we get:

CMSSW_12_2_1 -> Release
122X_mcRun3_2021_realistic_v9 -> Global Tag



=========== How to: collections stored in MINIAOD files ===========

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



=========== How to Produce the Big Ntuples/Trees (Local test + submission on CRAB) ===========

Work directory: $CMSSW_BASE/src/CMSDIJET/DijetRootTreeMaker/


The DijetTreeProducer.cc saves variables for two jet collections in the same tree, one with name "AK4" (AK4 CHS) and one with the name "AK8" (AK4 PUPPI). We use the flat-data-cfg_miniAOD_AK4CHSandAK4PUPPI.py and flat-MC-cfg_miniAOD_AK4CHSandAK4PUPPI.py files for data and MC.


Every time you log in to lxplus:

cd $CMSSW_BASE/src/
cmsenv
voms-proxy-init -voms cms


!!!! Before submitting to crab -> LOCAL RUN - CMS RUN !!!!

How to locally run a test in order to see if the code works as you wish before you send it to crab. (Example of running MC below)


Go to the flat-MC-cfg_miniAOD_AK4CHSandAK4PUPPI.py inside $CMSSW_BASE/src/CMSDIJET/DijetRootTreeMaker/prod/ : 

1) The file that will run locally is defined in L81. Go to the dataset in DAS and click "Files", choose one of the root files and put the root://cms-xrd-global.cern.ch/ in front

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

1)If you want to apply the JSON in the big trees (in Run3 we don't do that, we apply it in the reduced trees) put the correct JSON file ( For minimum Bias this step is commented out) -> Find the JSON file that correspond to data

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


Once all jobs are in finished mode, all big trees/ntuples will have appeared in the output file you have specified in EOS.


For DATA only: How to get the JSON from proccessed big trees

1) Open the "crab.log" file of the processed job

2) Search "Task name" and copy the part: 181113_164416:dkarasav_crab_JetHT__Run2018A-17Sep2018-v1__MINIAOD

3) Go to the directory that you gave on the on the "-d" argument (e.g. -d Output_JetHT_rereco_2018/ ) 

4) Do the command: crab remake --task=181113_164416:dkarasav_crab_JetHT__Run2018A-17Sep2018-v1__MINIAOD

5) Then a directory is created with name "crab_JetHT__Run2018A-17Sep2018-v1__MINIAOD/ ", so do the command: 

6) crab report crab_JetHT__Run2018A-17Sep2018-v1__MINIAOD

7) Inside it, in the directory "results" you find the JSON file with name: processedLumis.json

8) Rename the json file to have the name of the dataset





%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%% REDUCED TREES/NTUPLES %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

                
=========== Install CMS release ===========

cmsrel CMSSW_13_0_3
cd CMSSW_13_0_3/src
cmsenv
git clone -b Run3 https://github.com/CMSDIJET/DijetRootTreeAnalyzer.git CMSDIJET/DijetRootTreeAnalyzer
scram b -j 8

cd CMSDIJET/DijetRootTreeAnalyzer/
./scripts/make_rootNtupleClass.sh -f /path/to/big/trees/big_tree.root -t dijets/events

answer yes in the two questions.

This step will create the file rootNtupleClass.h in the /include folder

This command should be executed every time the stored variables in big trees change.



=========== Compile ===========

Choose the analyzer you want to use. Eg the analysisClass_mainDijetSelection_dijets_puppi_trigger_JetHTMET.C one (which is the same as the analysisClass_mainDijetSelection_dijets_puppi.C one but with extra TH1Ds saved for trigger efficiency studies)

ln -sf analysisClass_mainDijetSelection_dijets_puppi_trigger_JetHTMET.C src/analysisClass.C

ls -l src/analysisClass.C

to make sure that the link was successful. Then:

make clean
make

(whenever you change the a .C (or .h) file you should always do "make clean", "make")
No error message should appeared.



=========== How to run reduced trees/ntuples ===========

./main lists/path/to/list.txt config/cutFile_DIJET/cutFile_mainDijetSelection_wide_jets_puppi_trigger.txt dijets/events output/Name_of_file output/Name_of_file

if it runs fine in the output/ folder the file Name_of_file_reduced_skim.root will appear.

Always do "cmsenv" in the folder CMSSW_13_0_3/src/CMSDIJET/DijetRootTreeAnalyzer/ whenever you start to work 



Instructions on how to run the reduced trees in condor can be found here: https://github.com/dimkarasav/DijetRootTreeAnalyzer/blob/JetID/bash_scripts/README_reduced_trees_condor.txt



