CMS connect needed for slc6 environment:

$ ssh izisopou@login-el7.uscms.org
$ cmssw-slc6-condor		//to be in slc6 environment
$ git clone https://github.com/cms-sw/genproductions.git

For Run3 you can use LXPLUS:

mkdir GenerateGridpacks/
cd GenerateGridpacks
git clone https://github.com/cms-sw/genproductions.git

In genproductions/MetaData there is the pdflist_5f_2017.dat
Check https://lhapdf.hepforge.org/pdfsets.html for pdfs

For the Suu/Chi signals we used: 315200	NNPDF31_lo_as_0130 (+ CP2 tune)
So, go to pdflist_5f_2017.dat and add in the first line this:
315200 NNPDF31_lo_as_0130 101

and remove from the 3rd line from the end this:
315200 NNPDF31_lo_as_0130 1

cd genproductions/bin/MadGraph5_aMCatNLO/

Remove any Suu_Diquark_XXX gridpacks, log files and directories already there because it cannot overwrite and it crashes.

In cards/production/2017/13TeV/Suu_Diquark/template2/ we have the input cards.
In cards/production/2017/13TeV/Suu_Diquark/template2/Suu_Diquark_S2000_chi220/Suu_Diquark_S2000_chi220_run_card.dat replace:
50000 = nevents ! Number of unweighted events requested
with
1000 = nevents ! Number of unweighted events requested

Command to generate gridpack:

./gridpack_generation.sh <Name> cards/production/2017/13TeV/Suu_Diquark/template2/<Name>

where for example <Name> == Suu_Diquark_S2000_chi220

This makes the gridpacks. When it's done test it by generating LHE file:

mkdir test
cp Suu_Diquark_S2000_chi220_slc6_amd64_gcc700_CMSSW_10_2_24_patch1_tarball.tar.xz test/
cd test/
tar -xavf Suu_Diquark_S2000_chi220_slc6_amd64_gcc700_CMSSW_10_2_24_patch1_tarball.tar.xz   --> Unpacks the gridpack
./runcmsgrid.sh 20 5 5

This produces a cmsgrid_final.lhe file. 

To run multiple gridpacks in a loop:

$ python make_lhe_suu.py

You can fine that file made by Eva in: /home/izisopou/genproductions/bin/MadGraph5_aMCatNLO (in CMS connect)
It uses as input a csv file with all the mass points.


