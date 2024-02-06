Instructions on how to generate a gridpack, create an LHE file from it, convert the LHE file to a root tree and plot gen level distributions.


============================ Setup the code and take/modify necessary input cards ============================

mkdir GenerateGridpacks
cd GenerateGridpacks/
git clone https://github.com/cms-sw/genproductions.git

cd genproductions/bin/MadGraph5_aMCatNLO/

This will be your work area.

Input cards for Suu diquark signal at 13.6 TeV (narrow resonance for pp->Suu->(chi)(chi)->(ug)(ug)) are located in:
genproductions/bin/MadGraph5_aMCatNLO/cards/production/13p6TeV/Suu_Diquark/template

In the MassVals.csv write the M(S) and M(chi) masses and then run MakeCard.py: 
python MakeCard.py

This will create directories with the name Suu_Diquark_SXXX_chiYYY/ with the input cards for each mass point.

Useful info:
genproductions/bin/MadGraph5_aMCatNLO/cards/production/13p6TeV/Suu_Diquark/template/SuuDQTemplate_run_card.dat -> In lines 40,41 we define the center-of-mass energy

genproductions/bin/MadGraph5_aMCatNLO/cards/production/13p6TeV/Suu_Diquark/template/SuuDQTemplate_customizecards.dat -> Here we can modify the yuu and ychi couplings by adding in the first line something like this:

set FRBLOCK 1 0.681589
set FRBLOCK 2 1.02238

This sets λuu = yuu/2 = 0.681589 and λchi = ychi/2 = 1.02238 (default values for narrow resonances are 0.2 and 0.3 respectively).

For the asymmetric signals we may not have the factor of 2 and it could be that yuu = λuu ---> Please check!!


============================ How to create gridpacks ============================


Back in genproductions/bin/MadGraph5_aMCatNLO/ , the command to generate a gridpack is:

./gridpack_generation.sh <Name> cards/production/13p6TeV/Suu_Diquark/template/<Name>

where <Name> == Suu_Diquark_SXXX_chiYYY

To produce multiple gridpacks in a loop take the following script and modify it accordingly: 
/afs/cern.ch/user/i/izisopou/public/ForAmalia/gridpacks/generate_gridpacks_in_loop.py


If you don't get error messages and you get a message that the gridpack was created successfully, check also that it (.tar.xz) is around 47-48MB (do ls -lh to check). If it is significantly lower than that then there could be issues with the gridpack.


============================ How to create LHE files ============================


When the gridpack is successfully produced then in order to produce an LHE file from it do the following:

mkdir test/
cp Suu_Diquark_SXXX_chiYYY_slc7_amd64_gcc10_CMSSW_12_4_8_tarball.tar.xz test/
cd test/
tar -xavf Suu_Diquark_SXXX_chiYYY_slc7_amd64_gcc10_CMSSW_12_4_8_tarball.tar.xz  
./runcmsgrid.sh 1000 5 5

This will generate an LHE file with 1000 simulated signal events. There should be no errors; if there are then something is wrong with the gridpack.



============================ How to convert LHE files to root trees ============================

Below are the instructions I got from Eva.

Download ExRootAnalysis_V1.1.2.tar.gz from here: https://madgraph.mi.infn.it/Downloads/ExRootAnalysis/

or take it from here: /afs/cern.ch/user/e/evah/public/for_Ilias/ExRootAnalysis_V1.1.2.tar.gz

Then you need to untar the file (tar -xavf ExRootAnalysis_V1.1.2.tar.gz) and then cd into the ExRootAnalysis directory and type "make".  This will compile all the tools that are available.

To convert the LHE file do:
ExRootAnalysis/ExRootLHEFConverter name_of_file.lhe new_name.root

You will then have a tree with all the LHE events in the .root file.

You can then use your favorite way of analyzing trees. I have an example using MakeClass inherent to root, which then creates a .h and a .C file that you can use to loop over events and make the plots you want. For example, I used:
/afs/cern.ch/user/e/evah/public/for_Ilias/MakeChain.C
(which then creates a .C and .h file out of the input .root file)
Of course, you can also do this in python instead, if that’s what you prefer.  

In any case, I have an example of the analysis script for the RPV stops on lxplus:
/afs/cern.ch/user/e/evah/public/for_Ilias/Analysis.h
/afs/cern.ch/user/e/evah/public/for_Ilias/Analysis.C
Please note, the above example is specific to the RPV stop case.  
(i.e. it checks that the down and strange quarks are coming from the stops, and also makes plots for the radiated parton, etc.)
So, you would need to edit the .h by pointing it to your .root file, and edit the .C to loop over the events and make the plots that you want.

The relevant files for the Suu diquark are here:
/afs/cern.ch/user/i/izisopou/public/ForAmalia/gridpacks/Analysis.C
/afs/cern.ch/user/i/izisopou/public/ForAmalia/gridpacks/Analysis.h

Once you do that, you can run it by doing the following within root:

.L Analysis.C
Analysis t
t.Loop()


This will produce a root file (its output name is specified in Analysis.C) with the relevant histograms. Example of plotting script to then plot them:
/afs/cern.ch/user/i/izisopou/public/ForAmalia/gridpacks/Plot.C


