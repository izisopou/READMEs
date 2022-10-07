A few checks that we do to see if the fits behave well (regardless of the method used for the limit calculation). More specifically, we plot the ΔNLL surface as a function of r to see if it is smooth without discontinuities and the minimum agrees with the value taken from the fitDiagnostics. To do this you need to run the following command:

(1)
combine -M MultiDimFit card_combined.txt -n .name --rMin 0. --rMax 3 --algo grid --points 200 --cminDefaultMinimizerStrategy 0 --saveNLL --X-rtd REMOVE_CONSTANT_ZERO_POINT=1 --robustFit 1 --trackParameters rgx{.*} --saveSpecifiedIndex pdf_index 

This command calculates the profiled likelihood for 200 values of "r" between rMin and rMax. You can increase/decrease --points # to achieve a finer/rougher scan and you should also change rMax accordingly based on the best fit r (which you can get from fitDiagnostics) of the mass point you are checking.

After running the command a root file called higgsCombine.name.MultiDimFit.mH120.root is produced, from which you can then plot the ΔNLL of the envelope vs r. You can do this manually as follows:

(2)
TFile *fenv = new TFile("higgsCombine.name.MultiDimFit.mH120.root")
TCanvas *c1 = new TCanvas("c1","c1",1400,650);
c1 -> cd();
c1 -> Draw();
fenv -> cd();
limit -> SetMarkerColor(kBlack);
limit -> SetMarkerSize(1.5);
limit -> SetMarkerStyle(34);
limit->Draw("(deltaNLL+nll0+nll):r");


or you can use the following script:

(3)
plot1DScan.py  higgsCombine.name.MultiDimFit.mH120.root -o scan_DNLL_vs_r


Also, in the above command, the options " --trackParameters rgx{.*} --saveSpecifiedIndex pdf_index " allow to track all continuous nuisance parameters as well as the discrete pdf_index during the scan of the likelihood vs r, so you can also plot these parameters as a function of r or as a function of the likelihood to see if there are any discontinuities and if the best fit values agree with what you get from fitDiagnostics. For example, you can can quickly plot from the rootfile the trackedParam_jer vs r distribution to see the values of jer for each r scan by using (2) and substituting the last line with:

limit->Draw("trackedParam_jer:r","r>0 && r<0.2")

(changing 0.2 accordingly).

If you want to check the ΔNLL of an individual function instead of the envelope then you can add in the above command: 


--setParameters pdf_index=0 --freezeParameters pdf_index

This will freeze the pdf index to the function with the assigned number 0.



Apart from this, an additional test we like to do is to check the ΔNLL vs JER or JES in order to see if the minimum is indeed around 0 and the uncertainty around 1 since they are gaussian constrained. In order to do this you should add in the command (1) the options:

-P jer --setParameterRanges jer=-3,3 --floatOtherPOIs 1

and similarly for jes. This makes jer the POI and at the same time keeps the r floating because otherwise it fixes the r to the value (rMax-rMin)/2 (note also that with -P the newly assigned POI remains constrained, if it was previously constrained, whereas using --redefineSignalPOIs would remove the constraint. That's why we use -P here).  Then you can run the command (3) adding the option --POI jer (or jes) or use (2) changing the last line accordingly. The minimum of this distribution should agree with the best fit jer/jes taken from the fitDiagnostics.


*****

Secondly, with respect to the HybridNew limits, an additional check you can do is to plot the distributions of q_mu for the background and S+B toys, to see if these distributions look healthy and the statistics is adequate. You can do this running the following command:

python $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit/test/plotTestStatCLs.py --input [path to merged root file]  --poi r --val all --mass 125 

This will produce a root file with one canvas with the distributions for each value of "r" scanned.


Also, in case it helps, you can find here: /afs/cern.ch/user/d/dntounis/public/Rutgers the script " asymptotics_hybridnew_ratio_limits.C " (and the accompanying style script) with which you can plot the ratio of the asymptotics vs Full CLs limits (like we have in the AN for the resonant search) to see if it behaves as expected. You need only to change the mass point scans on top as well as the paths containing the rootfiles with the 6 limits per mass point (higgsCombine*.AsymptoticLimits.mH120.root, higgsCombine*.HybridNew.mH120.root). The 6 limits in the higgsCombine*.HybridNew.mH120.root file should be in the same order as in the higgsCombine*.AsymptoticLimits.mH120.root file which is done with the hadd command in the run_calculate_limits_HybridNew.sh script.


