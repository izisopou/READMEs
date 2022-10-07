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
