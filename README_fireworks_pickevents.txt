********* Instructions on how to setup fireworks *********

Instructions on how to download fireworks can be found in:

https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookFireworks 

mkdir Fireworks
cd Fireworks/
export SCRAM_ARCH=slc7_amd64_gcc820
cmsrel CMSSW_11_1_0
cd CMSSW_11_1_0/src/

wget https://cmsshow-rels.web.cern.ch/cmsShow-rels/cmsShow-11.1.2.1.linux.tgz
tar xzf cmsShow-11.1.2.1.linux.tgz

cd cmsShow-11.1.2.1/
cp /eos/user/i/izisopou/Fireworks/CMSSW_11_1_0/src/cmsShow-11.1.2.1/pickevents_EOY2017F_MINIAOD.root .
cp /eos/user/i/izisopou/Fireworks/CMSSW_11_1_0/src/cmsShow-11.1.2.1/pickevents_EOY2018A_MINIAOD.root .

(The first root file contains the published 4jet event from 2017 Run F. The second root file contains 3 events, the third of which is the second high mass 4jet event from 2018 Run A)

./cmsShow pickevents_EOY2017F_MINIAOD.root --no-version-check

You can find a very useful guide on how fireworks works here:

https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookFireworksUserGuide


********* Instructions on how to run pickevents *********

In order to create a root file to be used as input for fireworks do as follows:

cd in a CMSSW (I believe that CMSSW_10_2_X is good for processing EOY (pre-legacy) data, while CMSSW_10_6_X is needed for UL data, check also: https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookWhichRelease#DifferentReleases)

cd CMSSW_X_X_X/src
cmsenv
voms-proxy-init -voms cms

edmPickEvents.py "/JetHT/Run2017F-17Nov2017-v1/MINIAOD" 305814:610:971086788 --crab

where /JetHT/Run2017F-17Nov2017-v1/MINIAOD is the dataset and 305814:610:971086788 is the run:lumi:event of the event you want to pick.
The dataset that contains the second 4jet event (315721:151:200841184) is: /JetHT/Run2018A-17Sep2018-v1/MINIAOD, while the respective datasets for UL are: /JetHT/Run2017F-09Aug2019_UL2017-v1/MINIAOD and /JetHT/Run2018A-12Nov2019_UL2018-v2/MINIAOD

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














