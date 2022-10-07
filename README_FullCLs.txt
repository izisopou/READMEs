===== README for setup to run HybridNew limits/significance - final version for Paired Dijet Search ======
                                      =====  June 2021 =======

***Please make sure to setup CMSSW not in afs but in an area with a lot of space available, e.g. eos/cms/store, /eos/cms/work or cernbox (/eos/user/initial/username). After the grid jobs are finished they are transferred to CMSSW for further processing, so multiple GBs of space are required ***


-------------------------------------------------------------------------------------------------------------------------------------------


=== CMSSW Setup (same as in the previous readme)=====

Setup Release + Download from github combine+DijetAnalyzer

(Directions for currently recommended CMSSW release & tag can be found here: https://cms-analysis.github.io/HiggsAnalysis-CombinedLimit/ )

cd to where you want to setup your CMSSW release and do the following:


export SCRAM_ARCH=slc7_amd64_gcc700
cmsrel CMSSW_10_2_13
cd CMSSW_10_2_13/src
cmsenv
git clone https://github.com/CMSDIJET/DijetRootTreeAnalyzer CMSDIJET/DijetRootTreeAnalyzer
git clone -b dijetpdf_102X https://github.com/RazorCMS/HiggsAnalysis-CombinedLimit HiggsAnalysis/CombinedLimit
cd HiggsAnalysis/CombinedLimit


cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit
git fetch origin
git checkout v8.2.0
scramv1 b clean; scramv1 b # always make a clean build


# Get CombineHarvester - needed for Grid submissions

cd $CMSSW_BASE/src
git clone https://github.com/cms-analysis/CombineHarvester.git CombineHarvester
scram b

2. Now setup the RooParametricShapeBinPdfs needed for the analysis:

cd $CMSSW_BASE/src
cmsenv
cp /afs/cern.ch/user/d/dntounis/public/RooParametricShapeBinPdf_classes/February_2021/classes/cc_files/RooDijet5ParamBinPdf.cc HiggsAnalysis/CombinedLimit/src
cp /afs/cern.ch/user/d/dntounis/public/RooParametricShapeBinPdf_classes/February_2021/classes/cc_files/RooModDijet5ParamBinPdf.cc HiggsAnalysis/CombinedLimit/src

cp /afs/cern.ch/user/d/dntounis/public/RooParametricShapeBinPdf_classes/February_2021/classes/h_files/RooDijet5ParamBinPdf.h HiggsAnalysis/CombinedLimit/interface
cp /afs/cern.ch/user/d/dntounis/public/RooParametricShapeBinPdf_classes/February_2021/classes/h_files/RooModDijet5ParamBinPdf.h HiggsAnalysis/CombinedLimit/interface

cp /afs/cern.ch/user/d/dntounis/Fourjet_Fitting_Studies/CMSSW_10_2_13/src/HiggsAnalysis/CombinedLimit/src/RooAtlas5ParamBinPdf.cc HiggsAnalysis/CombinedLimit/src
cp /afs/cern.ch/user/d/dntounis/Fourjet_Fitting_Studies/CMSSW_10_2_13/src/HiggsAnalysis/CombinedLimit/interface/RooAtlas5ParamBinPdf.h HiggsAnalysis/CombinedLimit/interface

cd HiggsAnalysis/CombinedLimit/src
In the HiggsAnalysis/CombinedLimit/src folder modify the following files:

--> classes.h       : add the following lines:  
                                                #include "HiggsAnalysis/CombinedLimit/interface/RooAtlas5ParamBinPdf.h"
                                                #include "HiggsAnalysis/CombinedLimit/interface/RooModDijet5ParamBinPdf.h"

--> classes_def.xml : add the following lines:  	
                                                        <class name="RooAtlas5ParamBinPdf" /> 
                                                        <class name="RooModDijet5ParamBinPdf" /> 

Cd back to CMSSW_xx_x_xx/src/HiggsAnalysis/CombinedLimit and make a clean build:
 
cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit 
scramv1 b clean; scramv1 b


--------------------------------------------------------------------------------------------------------------------------------------------

==== Once you have produced the combined datacards in the output/ directory make the respective workspaces ====

cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/scripts/run_produce_workspaces.sh .

./run_produce_workspaces.sh


--------------------------------------------------------------------------------------------------------------------------------------------

=== Submit jobs to crab for Full CLs limits ===

cd $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/
 
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/scripts/run_grid_submissions_HybridNew_limits_v3.sh .
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/scripts/run_resubmit_grid_jobs_HybridNew_limits.sh . 
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/scripts/run_kill_grid_jobs_HybridNew.sh .
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/scripts/run_status_grid_jobs_HybridNew_limits.sh .
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/scripts/custom_crab_final_limits.py .

Modify the custom_crab_final_limits.py with the name of the directories to be created and then:

./run_grid_submissions_HybridNew_limits_v3.sh

!!! In order to determine the max values in the above code: 
To calculate the Full CLs limits you scan the signal strength r from 0 up to a max value that you need to determine in this step. Check from the asymptotic limits the +2σ limit value from combine (not in pb, the one you get from combine without having multiplied with the ref xsec) or the observed if the observed is larger than the +2σ limit. This will be more or less your max value in this step. For example if combine tells you that the +2σ limit is 0.028 then you can use max=0.03 as an upper value for this scan. The code is written in a way that 200 values are scanned from 0 up to max  

-T 200 -i 10 -s 10148:10152:1 means: 10 iterations of 200 toys = 2000 toys x 5 (seed 10148, 10149, 10150, 10151, 10152) = 10,000 toys generated for each one of the 200 scans of signal strength

!!!


== Print status of grid jobs  ==

./run_status_grid_jobs_HybridNew.sh


== Resubmit jobs on the Grid for limits/significance ===

./run_resubmit_grid_jobs_HybridNew.sh


== Kill grid jobs (if necessary) ==

./run_kill_grid_jobs_HybridNew.sh


---------------------------------------------------------------------------------------------------------------------------------------------


=== Process results from the Grid once all jobs are in finished status ===


cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/scripts/run_getoutput_from_grid_jobs.sh .
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/scripts/run_untar_hadd_grid_jobs.sh .
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/scripts/run_calculate_limits_HybridNew.sh .


1. Getoutput (transfer tar files from eos to CMSSW)

./run_getoutput_from_grid_jobs.sh

This command will bring in the results/ folder in CMSSW the output tar files from eos (they are usually 1005, 1010, 1015 or 1020 depending on how many jobs there are)


2. Untar and hadd tar files

./run_untar_hadd_grid_jobs.sh

This command will untar all the above files, fetching from each tar file a root file, and then all root files are hadded into a single root file that will be used next for the limit calculation.


3. Calculate limits/significance with the final (hadded) input files from the Grid

./run_calculate_limits_HybridNew.sh

This command takes ~1 hour for each mass point (consider using screens or multiple LXPLUS to finish quicker)








