
===== README for setup to run HybridNew limits/significance - final version for Paired Dijet Search ======
                                      =====  June 2021 =======

***Please make sure to setup CMSSW not in afs but in an area with a lot of space available, e.g. eos/cms/store, /eos/cms/work or cernbox (/eos/user/initial/username). After the grid jobs are finished they are transferred to CMSSW for further processing, so multiple GBs of space are required ***



=== CMSSW Setup =====

1.Setup Release + Download from github combine+DijetAnalyzer

(Directions for currently recommended CMSSW release & tag can be found here: https://cms-analysis.github.io/HiggsAnalysis-CombinedLimit/ )

cd to where you want to setup your CMSSW release and do the following:


export SCRAM_ARCH=slc7_amd64_gcc700
cmsrel CMSSW_10_2_13
cd CMSSW_10_2_13/src
cmsenv
git clone https://github.com/CMSDIJET/DijetRootTreeAnalyzer CMSDIJET/DijetRootTreeAnalyzer
git clone -b dijetpdf_102X https://github.com/RazorCMS/HiggsAnalysis-CombinedLimit HiggsAnalysis/CombinedLimit
cd HiggsAnalysis/CombinedLimit
scramv1 b clean; scramv1 b -j 4 # always make a clean build


# Get CombineHarvester - needed for Grid submissions

cd $CMSSW_BASE/src
git clone https://github.com/cms-analysis/CombineHarvester.git CombineHarvester
scram b -j 4

# Ignore the errors at the end, they concern DijetRootTreeAnalyzer, CombineHarvester compiles just fine

2. Now setup the RooParametricShapeBinPdfs needed for the analysis:

cd $CMSSW_BASE/src
cmsenv
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/dimitris_ntounis/HybridNew/scripts_final/RooDijet5ParamBinPdf.cc HiggsAnalysis/CombinedLimit/src
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/dimitris_ntounis/HybridNew/scripts_final/RooModDijet5ParamBinPdf.cc HiggsAnalysis/CombinedLimit/src

cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/dimitris_ntounis/HybridNew/scripts_final/RooDijet5ParamBinPdf.h HiggsAnalysis/CombinedLimit/interface
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/dimitris_ntounis/HybridNew/scripts_final/RooModDijet5ParamBinPdf.h HiggsAnalysis/CombinedLimit/interface

cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/dimitris_ntounis/HybridNew/scripts_final/RooAtlas5ParamBinPdf.cc HiggsAnalysis/CombinedLimit/src
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/dimitris_ntounis/HybridNew/scripts_final/RooAtlas5ParamBinPdf.h HiggsAnalysis/CombinedLimit/interface

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
scramv1 b clean; scramv1 b -j 4


=== Transfer files needed for the Fourjet analysis ===

1. Get final datacards/workspaces:

cd $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer
cp -avr /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/datacards/13slices_Envelope_3_func_plots_alpha0p* output/ 


2. Copy  scripts:


cd $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer

cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/dimitris_ntounis/HybridNew/scripts_final/custom_crab_final_limits.py     .
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/dimitris_ntounis/HybridNew/scripts_final/custom_crab_final_significance.py     .
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/dimitris_ntounis/HybridNew/scripts_final/run_calculate_limits_HybridNew_final.sh     .
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/dimitris_ntounis/HybridNew/scripts_final/run_calculate_significance_HybridNew_final.sh     .
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/dimitris_ntounis/HybridNew/scripts_final/run_getoutput_from_grid_jobs_HybridNew.sh     .
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/dimitris_ntounis/HybridNew/scripts_final/run_grid_submissions_HybridNew_limits_final.sh     .
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/dimitris_ntounis/HybridNew/scripts_final/run_grid_submissions_HybridNew_significance_final.sh      .
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/dimitris_ntounis/HybridNew/scripts_final/run_status_grid_jobs_HybridNew.sh     .
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/dimitris_ntounis/HybridNew/scripts_final/run_untar_hadd_grid_jobs_HybridNew_final.sh      .

       
3. chmod shell scripts:

chmod +x run_calculate_limits_HybridNew_final.sh
chmod +x run_calculate_significance_HybridNew_final.sh
chmod +x run_getoutput_from_grid_jobs_HybridNew.sh
chmod +x run_grid_submissions_HybridNew_limits_final.sh
chmod +x run_grid_submissions_HybridNew_significance_final.sh
chmod +x run_status_grid_jobs_HybridNew.sh
chmod +x run_untar_hadd_grid_jobs_HybridNew_final.sh
                
           
               
          
   
            






=== Run Limits/Significance/FitDiagnostics on combined datacards to test everything is ok ====


combine -M Significance output/13slices_Envelope_3_func_plots_alpha0p25/card_combined_8400_2100.txt     --rMin 0 --rMax 0.1 --saveWorkspace

-> Make sure you get : 3.61842 


combine -M AsymptoticLimits output/13slices_Envelope_3_func_plots_alpha0p23/card_combined_7000_1610.txt  --setParameterRanges r=0,0.1 --saveWorkspace --cminDefaultMinimizerTolerance 0.00100 --cminDefaultMinimizerStrategy 2 --strictBounds

-> Make sure you get :

 -- AsymptoticLimits ( CLs ) --
Observed Limit: r < 0.0025
Expected  2.5%: r < 0.0011
Expected 16.0%: r < 0.0015
Expected 50.0%: r < 0.0028
Expected 84.0%: r < 0.0046
Expected 97.5%: r < 0.0081



combine -M FitDiagnostics --robustFit 1 output/13slices_Envelope_3_func_plots_alpha0p27/card_combined_4000_1080.txt --rMin 0 --rMax 1    --setCrossingTolerance 0.00001 --cminDefaultMinimizerStrategy=0 

-> Make sure you get : 

 --- FitDiagnostics ---
Best fit r: 0.029372  -0.015268/+0.0170316  (68% CL)



If the results above agree, you should be all setup!


== Submit jobs on the Grid for limits/significance ===


./run_grid_submissions_HybridNew_limits_final.sh
./run_grid_submissions_HybridNew_significance_final.sh



== Print status/resubmit  grid jobs  ==


./run_status_grid_jobs_HybridNew.sh







Once all jobs are finished move on with the processing:

== Process results from the Grid ==

-----------------------------------------------------------------------------------------------------------------------------------------
1. Getoutput (transfer tar files from eos to CMSSW)

./run_getoutput_from_grid_jobs_HybridNew.sh

This will take many hours for all 56 mass points (4.5 - 10 TeV). Therefore it is better to open 4 different LXPLUS (so that their resources are independent) and run 14 mass points in each one. (Had tried screens but was getting crab errors).

It is common that not all .tar files are successfully retrieved from eos to CMSSW in the first run of this command. In order to quickly check afterwards for which mass points not all .tar files are retrieved do:

cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/scripts/run_check_getoutput.sh .
chmod +x run_check_getoutput.sh
./run_check_getoutput.sh

This will print for each mass point the number of files that are located in the result/ folder. There should be 1005 or 1010 or 1015 or 1020 .tar files there, depending on how many jobs this particular mass point had. If you see a number other than that this means that less files were retrieved so you will have to rerun the ./run_getoutput_from_grid_jobs_HybridNew.sh command. Notice that the getoutput command skips the files that have already been retrieved and only retrieves the rest of the files so that will take much less time than before.

-----------------------------------------------------------------------------------------------------------------------------------------

2. Untar and hadd tar files

./run_untar_hadd_grid_jobs_HybridNew_final.sh

This command will probably not take more than ~2-3 hours so it is fine to just run it for all mass points as is. However sometimes LXPLUS lags a lot and you will have to split it in several LXPLUS to run it quicker. In order to quickly check if the untar and hadd was successful for every mass point do again:

./run_check_getoutput.sh

If for example you had 1010 jobs in this particular mass point, you will need to have in the result/ folder 2021 files (1010 .tar files + 1010 .root files from the untar + 1 hadded .root file). Same for when the mass point had 1005 or 1015 or 1020 jobs. If you see something else printed you will have to check, it probably means that something wasn't produced properly.

Also sometimes the hadded files are produced but they have not closed properly! In order to quickly check this go to the website of cernbox https://cernbox.cern.ch/index.php/apps/files/?dir=/&
and view the size of each mass point directory. The hadded root files are usually ~80-90MB so if they haven't closed properly for a certain mass point the corresponding directory will have significantly smaller size. 

-----------------------------------------------------------------------------------------------------------------------------------------

3. Calculate limits/significance with the final (hadded) input files from the Grid

./run_calculate_limits_HybridNew_final.sh

This command takes ~1 hour for each mass points so you would need 56 hours in total! Instead:

cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/scripts/run_jobs_on_screens_arguments.sh .
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/scripts/run_calculate_limits_HybridNew_screens.sh .
chmod +x run_jobs_on_screens_arguments.sh
chmod +x run_calculate_limits_HybridNew_screens.sh

./run_jobs_on_screens_arguments.sh

This will open one screen for each mass point and will run them in parallel. However, opening 56 screens at the same time might be too cpu consuming so you will need to open 2 different LXPLUS and run 28 mass points in each one (28 screens + 28 screens).


./run_calculate_significance_HybridNew_final.sh

On the contrary to calculating limits, this command doesn't take too much time so you can run it as it is.

-----------------------------------------------------------------------------------------------------------------------------------------

******* Running the HybridNew commands create a lot of files so after you are *fully done* with processing and calculating limits you will have to tar the un-needed files *******


cd to the eos path where the folder with the crab outputs are and do:

tar -czvf file.tar.gz directory/

This will create a file.tar.gz file that contains all the files that are inside the directory/ folder. Then delete the folder to free up space:

rm -rf directory/

Then go to the CMSSW directory:
cd $CMSSW_BASE/src/CMSDIJET/DijetRootTreeAnalyzer/
cp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/scripts/run_tar_files.sh .
chmod +x run_tar_files.sh

./run_tar_files.sh

Inside each results/ folder for every mass point there are 1000 .tar files, 1000 .root files and 1 hadded (merged) .root file. Only the hadded is truly needed for calculating limits so with the above script you create 1 tar.gz file from the 1000 .tar files and 1 tar.gz from the 1000 .root files. Then rerun the above command while now you comment out the tar commands and un-comment out the rm commands. In the end, you will end up with only 3 files in each results/ folder (combine_output.tar.gz, higgsCombine.tar.gz and the hadded root file)


!!!!! Useful commands in order to check whether the tar was successful before you rm the files !!!!!

1) Check how many files there are inside a folder:

find [path to directory] -type f | wc -l

2) Check how many files there are inside a .tar.gz file:

gzip -cd file.tar.gz | tar -tvv | grep -c ^-









