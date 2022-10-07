This is to run the analysis in CMSSW_12_2_1:

tcsh
cmsrel CMSSW_12_2_1
cd CMSSW_12_2_1/src
cmsenv
git cms-init
git clone https://github.com/CMSDIJET/DijetRootTreeMaker.git CMSDIJET/DijetRootTreeMaker
git clone https://github.com/CMSDIJET/DijetRootTreeAnalyzer.git CMSDIJET/DijetRootTreeAnalyzer
scram b -j 8

  
For the TreeMaker it pop ups the following error: 

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

write:

DijetTreeProducer::DijetTreeProducer(edm::ParameterSet const& cfg)
: triggerCache_(cfg.getParameterSet("triggerConfiguration"),consumesCollector()),
noiseFilterCache_(cfg.getParameterSet("noiseFilterConfiguration"),consumesCollector())
{ 


We should also fix the python scripts so that python3 will not give compilation errors. This includes changing tabs to spaces, putting parentheses in print and exec statements and making the following change:

for k, g in groupby(enumerate(sidebandBins), lambda (i,x):i-x):  --->  for k, g in groupby(enumerate(sidebandBins), lambda i,x:i-x):

Get the updated python scripts from here:

/afs/cern.ch/work/n/nsaoulid/public/CMSSW_12_2_1/src/CMSDIJET/DijetRootTreeAnalyzer/


%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

When running a local test for the big trees with cmsRun you will get the following error:

----- Begin Fatal Exception 19-Apr-2022 12:49:41 CEST-----------------------
An exception of category 'Configuration' occurred while
   [0] Constructing the EventProcessor
   [1] Constructing module: class=DijetTreeProducer label='dijets'
Exception Message:
MissingParameter: Parameter 'usePathStatus' not found.
----- End Fatal Exception -------------------------------------------------


To solve this you need to go to the .py you run with cmsRun and add the following line:

usePathStatus = cms.bool( True ),

right below the following 2 lines:

triggerConfiguration = cms.PSet(
  
and

noiseFilterConfiguration = cms.PSet(

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%


For the reduced trees:

1) In the include/baseClass.h:

add the

#include <TObjString.h>

2) Take the file: /afs/cern.ch/work/n/nsaoulid/public/ForIlias/Makefile 

3) If you are getting an error about BTagCalibration while compiling with "make" comment out in the analyzer the lines:

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


