
********* Instructions on how to run pickevents *********

In order to create a MiniAOD root file (to be used as input for fireworks) do as follows:

cd in a CMSSW 

CMSSW_10_2_X for Run2 pre-Legacy data
CMSSW_10_6_X for Run2 Legact data (UL)
CMSSW_12_3_6 for Run3 data

cd CMSSW_X_X_X/src
cmsenv
voms-proxy-init -voms cms

edmPickEvents.py "/JetHT/Run2017F-17Nov2017-v1/MINIAOD" 305814:610:971086788 --crab

where /JetHT/Run2017F-17Nov2017-v1/MINIAOD is the dataset and 305814:610:971086788 is the run:lumi:event of the event you want to pick.
The dataset that contains the second 4jet event (315721:151:200841184) is: /JetHT/Run2018A-17Sep2018-v1/MINIAOD

If you want the output root file to have more than one event then do:

edmPickEvents.py "/JetHT/Run2017F-17Nov2017-v1/MINIAOD" events.txt --crab

where events.txt is a text file with one event in each line with the format run:lumi:event

The above command automatically generates a "pickevents_crab.py" script, a "pickevents_runEvents.txt" file with the run and event number of the event(s) and a "pickevents.json" with the lumi of the event(s).

You will then have to modify the "pickevents_crab.py" script before you sumbit the job(s) to crab. If you change the name of the generated "pickevents_runEvents.txt" you will have to change it in the .py script as well. You can also modify the name of the output root file, the name of the workarea folder etc.

You will also have to add the following command in the .py script:

config.JobType.allowUndistributedCMSSW = True

Add it under the "JobType" section.

After you save all the changes to the .py submit the jobs by doing:

crab submit -c pickevents_crab.py

Check the status of the jobs with:

crab status crab_pickevents_XXXX_YYYY/crab_pickEvents/

When jobs are finished do:

crab getoutput crab_pickevents_XXXX_YYYY/crab_pickEvents/ --checksum=no 

The output root file to be used as input for fireworks is located in the folder crab_pickevents_XXXX_YYYY/crab_pickEvents/results/














