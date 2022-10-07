******************************** Instructions for creating big ntuples (big trees) ******************************** 

We search for the datasets in DAS: https://cmsweb.cern.ch/das/

e.g. we type in the search bar: dataset status=* dataset=/Suu_Diquark*/*v14-v*/MINIAODSIM

In each dataset we want to process we click the "Release" and the "Config" buttons to see the CMSSW release and the Global Tag (GT). E.g. for one of the above datasets: we get:

CMSSW_9_4_7 -> Release
94X_mc2017_realistic_v14 -> Global Tag

In order to see which jet collections are stored in a MINIAOD dataset:
1) Find the dataset in DAS, click "Files" and choose one of the root files
2) Under a CMSSW release in your lxplus do:

cmsenv
voms-proxy-init -voms cms
edmDumpEventContent root://cms-xrd-global.cern.ch//store/blabla

where /store/blabla is the root file from DAS

This will print the jet collections. For example:

slimmedJets 		-> reco AK4CHS jets
slimmedJetsPuppi 	-> reco AK4PUPPI jets
slimmedJetsAK8 	-> reco AK8PUPPI jets

slimmedGenJets 	-> gen AK4 jets
slimmedGenJetsAK8	-> gen AK8 jets

See: https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookMiniAOD2017#Jets


============== Part 1: github, public ssh key -> done once for your lxplus account, you can skip this part if you already have done this in the past ===============
 
To do some of the following commands on github you will need a "public ssh key" installed to your lxplus account.

1)Follow the orders to get an ssh key from the link: 

https://git-scm.com/book/en/v2/Git-on-the-Server-Generating-Your-SSH-Public-Key

2) Then you need to add it to your github account (if you don't have an account you need to make one) . Follow the links to add the key to your account here: 

https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/

3) Now you need to connect your lxplus account to  your github account: (https://twiki.cern.ch/twiki/bin/view/Sandbox/ASmallGitCMSSWTutorial)

Do it with these commands: 

Run following commands:
git config --global user.name "First name Last name"
git config --global user.email <Your-Email-Address>
git config --global user.github <Your-Just-Created-GitHub-Account-Username>


4) Finally, you need to copy the key from your local pc to your lxplus account: 

scp -r /home/dkarasav/.ssh/id_rsa.pub "dkarasav@lxplus.cern.ch:~/.ssh/
scp -r /home/dkarasav/.ssh/id_rsa "dkarasav@lxplus.cern.ch:~/.ssh/

This needs to be done one once. After then you just need to remember and give the "passphraze" you defined when generating your key.

Check if your lxplus is connect to the github account:

eval $(ssh-agent); ssh-add /path/to/your/key
ssh -T git@github.com

HINT: there are *plenty* of instructions on the internet on how to generate an "ssh public key" (steps 1, 2)  locally and there are 2-3 - or more - ways of doing it.
Find one that suits you (if this doesnt) and dont forget to copy the key once you get it to your lxplus account. 




========================= Part 2: Get The treemaker from github ( done every time you need a new CMSSW release) =========================


Instructions for TreeMaker:

tcsh
setenv SCRAM_ARCH slc7_amd64_gcc700
cmsrel CMSSW_10_1_5
cd CMSSW_10_1_5/src
cmsenv
git cms-init

//Do not do the following 3 commands, now the correc Puppi weights (v15) are stored in MiniAOD
//git cms-addpkg PhysicsTools/PatAlgos
//git remote add ahinzmann git@github.com:ahinzmann/cmssw.git
//git fetch ahinzmann puppiWeightedMultiplicities


The standard instructions for the tree maker are here:
https://github.com/CMSDIJET/DijetRootTreeMaker/blob/master/instructions/bigtuples_instructions_data.txt

Download the codes and compile:

git clone https://github.com/CMSDIJET/DijetRootTreeMaker.git CMSDIJET/DijetRootTreeMaker
scram b -j 8

cp /afs/cern.ch/user/i/izisopou/public/ForDimitrisNtounis/4jets/TreeMaker/flat-MC-cfg_miniAOD_latest.py $CMSSW_BASE/src/CMSDIJET/DijetRootTreeMaker/prod/
 
 
 
For CMSSW_12_2_1 it pop ups the following error: 

/eos/home-i/izisopou/4jets_Run3/CMSSW_12_2_1/src/CMSDIJET/DijetRootTreeMaker/plugins/DijetTreeProducer.cc:81:112: error: use of deleted function 'triggerExpression::Data& triggerExpression::Data::operator=(triggerExpression::Data&&)'
   81 |   triggerCache_       = triggerExpression::Data(cfg.getParameterSet("triggerConfiguration"),consumesCollector());
      |                                                                                                                ^
In file included from /eos/home-i/izisopou/4jets_Run3/CMSSW_12_2_1/src/CMSDIJET/DijetRootTreeMaker/plugins/DijetTreeProducer.h:9,
                 from /eos/home-i/izisopou/4jets_Run3/CMSSW_12_2_1/src/CMSDIJET/DijetRootTreeMaker/plugins/DijetTreeProducer.cc:16:
/cvmfs/cms.cern.ch/slc7_amd64_gcc900/cms/cmssw/CMSSW_12_2_1/src/HLTrigger/HLTcore/interface/TriggerExpressionData.h:23:9: note: 'triggerExpression::Data& triggerExpression::Data::operator=(triggerExpression::Data&&)' is implicitly deleted because the default definition would be ill-formed:
   23 |   class Data {
      |         ^~~~


To solve this, in the code CMSDIJET/DijetRootTreeMaker/plugins/DijetTreeProducer.cc comment out the following lines:
triggerCache_       = triggerExpression::Data(cfg.getParameterSet("triggerConfiguration"),consumesCollector());
noiseFilterCache_   = triggerExpression::Data(cfg.getParameterSet("noiseFilterConfiguration"),consumesCollector());

and in the beginning, instead of 

DijetTreeProducer::DijetTreeProducer(edm::ParameterSet const& cfg)
{

do:

DijetTreeProducer::DijetTreeProducer(edm::ParameterSet const& cfg)
: triggerCache_(cfg.getParameterSet("triggerConfiguration"),consumesCollector()),
noiseFilterCache_(cfg.getParameterSet("noiseFilterConfiguration"),consumesCollector())
{ 
 

========================= Part 3: Do a local test and then run jobs in CRAB =========================

Every time you log in to lxplus:

cd $CMSSW_BASE/src/
cmsenv
voms-proxy-init -voms cms


!!!! Before submitting to crab -> LOCAL RUN - CMS RUN !!!!

How to locally run a test in order to see if the code works as you wish before you send it to crab. (Example of running MC below)


Go to the flat-MC-cfg_miniAOD_latest.py inside $CMSSW_BASE/src/CMSDIJET/DijetRootTreeMaker/prod/ : 

1) The file that will run localy is defined in line 109. Go to the dataset in DAS and click "Files", choose one of the root files and put the root://cms-xrd-global.cern.ch/ in front

2) Find the Global Tag of the dataset and put it in L14. Comment out L13.

3) Uncomment out  line 27:  fileName=cms.string('test.root') and comment out line 26.

4) Go to L19 and choose how many events to process as a test

Run the code by executing:

cmsRun flat-MC-cfg_miniAOD_latest.py

Remember to change the lines as they were for 2) and 3) in order to run on crab. 


Similarly you can do a local test with flat-data-cfg_miniAOD_latest.py

If the big tree test.root is produced properly (with the amount of events you selected) then everything is okay.



I) RUN DATA ON CRAB

cd $CMSSW_BASE/src/CMSDIJET/DijetRootTreeMaker/prod/submitJobsWithCrab3/

The command to submit jobs to crab is the following:

=======================================================================
python createAndSubmitDATA.py -d Output -v 2018_data -i Inputs/InputList.txt  -t crab3_template_data.py -c ../flat-data-cfg_miniAOD.py -n $USER --submit
========================================================================

Explanation of the above command and what you need to do before you execute it:

Inside $CMSSW_BASE/src/CMSDIJET/DijetRootTreeMaker/prod/submitJobsWithCrab3/ you create beforehand the folder: Output/

Once you submit the jobs, the crab task will be in a directory : /Output/2018_data_20220113_1905/ (-v 2018_data creates automatically the folder 2018_data_20220113_1905 where the last part is the date and time you submitted the task)


You create beforehand a folder: Inputs/ and inside it a .txt InputList.txt -> In this txt you need to write something like the following line:

/MinimumBias/Run2018C-PromptReco-v3/MINIAOD -1 1  101X_dataRun2_Prompt_v11

This corresponds to:  Name of dataset , how many events to process (-1 to run all events), how many files per job, Global Tag 

If you want to process more than one datasets, you can have multiple of those lines.

In the script crab3_template_data.py :

1)Put the correct JSON file ( For minimum Bias this step is commented out) -> Find the JSON file that correspond to data

 config.Data.lumiMask = '/afs/cern.ch/work/m/mdiamant/private/CMSSW_9_2_13_TreeMaker/src/CMSDIJET/DijetRootTreeMaker/prod/submitJobsWithCrab3/JSON_Certified/Cert_294927-305636_13TeV_PromptReco_Collisions17_JSON.txt' (you do not usually run min bias with a JSON file)
                      
2) Put the output directory to save the big trees
 
config.Data.outLFNDirBase = '/store/group/phys_jetmet/magda/bigtrees_test/'


II) RUN MC ON CRAB

Same logic as for data. To submit jobs do:

=======================================================
python createAndSubmitMC.py -d Output/ -v Diquark_signals -i Inputs/InputList.txt -t crab3_template_MC.py -c ../flat-MC-cfg_miniAOD_latest.py -n $USER --submit
=======================================================

We do not have a JSON file here in MC, so in the crab3_template_MC.py just change the output directory

Check status of jobs:

crab status Output/Diquark_signals_YYYYMMDD_HHMM/

Resubmit jobs:

crab resubmit Output/Diquark_signals_YYYYMMDD_HHMM/



=================================== Get the JSON from proccessed big trees (Data) ===============================

1) Open the "crab.log" file of the processed job

2) Search "Task name" and copy the part: 181113_164416:dkarasav_crab_JetHT__Run2018A-17Sep2018-v1__MINIAOD

3) Go to the directory that you gave on the on the "-d" argument (e.g. -d Output_JetHT_rereco_2018/ ) 

4) Do the command: crab remake --task=181113_164416:dkarasav_crab_JetHT__Run2018A-17Sep2018-v1__MINIAOD

5) Then a directory is created with name "crab_JetHT__Run2018A-17Sep2018-v1__MINIAOD/ ", so do the command: 

6) crab report crab_JetHT__Run2018A-17Sep2018-v1__MINIAOD

7) Inside it, in the directory "results" you find the JSON file with name: processedLumis.json

8) Rename the json file to have the name of the dataset




