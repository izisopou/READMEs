python python/WriteDataCard_4J_envelope_inclusive_Jim.py -b PFDijet2017_4J -c config/Configfiles_inclusive_Envelope_3_functions_upto9067/fourjet_inclusive_multipdf_3_func_startfrom1770.config -m Suu --model2 Chi --mass 2000 --mass2 500 /eos/user/i/izisopou/4jets/CMSSW_10_2_13/src/CMSDIJET/DijetRootTreeAnalyzer/inputs/rootfiles_inclusive/data_inclusive/HISTOS_4J_Alldata_multby1000_SR_alphagt0p1.root /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets/signals_13bins/alpha0p25/nominal/ResonanceShapes_Suu2000_Chi500_SR_noslices.root --jesUp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets/signals_13bins/alpha0p25/jes_up/ResonanceShapes_Suu2000_Chi500_SR_noslices.root  --jesDown /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets/signals_13bins/alpha0p25/jes_down/ResonanceShapes_Suu2000_Chi500_SR_noslices.root --jerUp /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets/signals_13bins/alpha0p25/jer_up/ResonanceShapes_Suu2000_Chi500_SR_noslices.root --jerDown /eos/cms/store/group/phys_exotica/dijet/Dijet13TeV/ilias/4jets/signals_13bins/alpha0p25/nominal/ResonanceShapes_Suu2000_Chi500_SR_noslices.root -d output/Study_for_Ali_SplitDataset/Correct_Lumi_S2000_chi500_MultBy1000_startfrom1770/ --xsec 0.001 --lumi 137000000 --multi


--------------------------------------------------------------------------------------------------------------------------------------------------------

combine -M MultiDimFit output/inclusive_Envelope_3_func_plots_alpha0p25/dijet_combine_Suu_Chi_2000_500_lumi-137.000_PFDijet2017_4J.txt --cminDefaultMinimizerStrategy 0 --saveWorkspace --setParameters r=0 --freezeParameters r -n .scenario1

combine -M Significance higgsCombine.scenario1.MultiDimFit.mH120.root --snapshotName MultiDimFit -t -1 --expectSignal 0 --saveToys -n .scenario1

combine -M AsymptoticLimits higgsCombine.scenario1.MultiDimFit.mH120.root --snapshotName MultiDimFit -t -1 --expectSignal 0 --saveToys -n .scenario1


--------------------------------------------------------------------------------------------------------------------------------------------------------

combine -M MultiDimFit higgsCombine.scenario1.MultiDimFit.mH120.root --snapshotName MultiDimFit -t -1 --expectSignal 2 --saveToys --algo grid --points 500 --cminDefaultMinimizerStrategy 0 --saveNLL --X-rtd REMOVE_CONSTANT_ZERO_POINT=1 --rMin 0. --rMax 4. -n .scenario1.expSig0p02pb.nll

combine -M MultiDimFit higgsCombine.scenario1.MultiDimFit.mH120.root --snapshotName MultiDimFit -t -1 --expectSignal 2 --saveToys --algo grid --points 500 --cminDefaultMinimizerStrategy 0 --saveNLL --X-rtd REMOVE_CONSTANT_ZERO_POINT=1 --rMin 0. --rMax 4. -n .scenario1.expSig0p02pb.nll.StatOnly --freezeParameters jer,jes,lumi,p1_PFDijet2017_4J,p2_PFDijet2017_4J,p3_PFDijet2017_4J,p4_PFDijet2017_4J,pa1_PFDijet2017_4J,pa2_PFDijet2017_4J,pa3_PFDijet2017_4J,pa4_PFDijet2017_4J,pmd1_PFDijet2017_4J,pmd2_PFDijet2017_4J,pmd3_PFDijet2017_4J,pmd4_PFDijet2017_4J,shapeBkg_PFDijet2017_4J_multi_PFDijet2017_4J__norm


python plot1DScan.py  higgsCombine.scenario1.expSig0p02pb.nll.MultiDimFit.mH120.123456.root --others higgsCombine.scenario1.expSig0p02pb.nll.StatOnly.MultiDimFit.mH120.123456.root:StatOnly:2 -o scenario1_nominalstats --y-max 40 --main-color 1 --main-label Stat+Syst --y-cut 40


--------------------------------------------------------------------------------------------------------------------------------------------------------

TFile *f = new TFile("higgsCombine.scenario1.MultiDimFit.mH120.root");
RooWorkspace* w = (RooWorkspace*)(f->Get("w"));
RooRealVar* th1x = w->var("th1x");
TFile *f1 = new TFile("higgsCombine.scenario1.expSig2.Significance.mH120.123456.root");
RooDataSet* asim = (RooDataSet*)(f1->Get("toys/toy_asimov"));
TH1F *h = (TH1F*)asim->createHistogram("th1x");
h->Draw("histo");
c1->SetLogy(1)

or 



TFile *f = new TFile("higgsCombine.multby1000.scenario1.MultiDimFit.mH120.root");

RooWorkspace* w = (RooWorkspace*)(f->Get("w"));
RooRealVar* th1x = w->var("th1x");
TFile *f1 = new TFile("higgsCombine.multby1000.scenario1.expSig0.AsymptoticLimits.mH120.123456.root");
RooDataSet* asim1 = (RooDataSet*)(f1->Get("toys/toy_asimov"));
RooPlot* frame = w->var("th1x")->frame()
asim1->plotOn(frame, DataError(RooAbsData::Poisson))


TFile *f2 = new TFile("higgsCombine.multby1000.scenario2.expSig0.AsymptoticLimits.mH120.123456.root");
RooDataSet* asim2 = (RooDataSet*)(f2->Get("toys/toy_asimov"));
asim2->plotOn(frame, DataError(RooAbsData::Poisson), MarkerColor(kRed))

TFile *f3 = new TFile("higgsCombine.multby1000.scenario3.expSig0.AsymptoticLimits.mH120.123456.root");
RooDataSet* asim3 = (RooDataSet*)(f3->Get("toys/toy_asimov"));
asim3->plotOn(frame, DataError(RooAbsData::Poisson), MarkerColor(kBlue))

TFile *f4 = new TFile("higgsCombine.multby1000.scenario4.expSig0.AsymptoticLimits.mH120.123456.root");
RooDataSet* asim4 = (RooDataSet*)(f4->Get("toys/toy_asimov"));
asim4->plotOn(frame, DataError(RooAbsData::Poisson), MarkerColor(kMagenta))

frame->Draw()
c1->SetLogy(1)

TFile *f5 = new TFile("Split_Datasets_Study/higgsCombine.scenario1.expSig0.Significance.mH120.123456.root");
RooDataSet* asim5 = (RooDataSet*)(f5->Get("toys/toy_asimov"));
asim5->plotOn(frame, DataError(RooAbsData::Poisson), MarkerColor(kGray))


combine -M AsymptoticLimits higgsCombine.scenario1.MultiDimFit.mH120.root --snapshotName MultiDimFit -t -1 --expectSignal 2 --saveToys -n .scenario1.expSig0p02pb.StatOnly --freezeParameters jer,jes,lumi,p1_PFDijet2017_4J,p2_PFDijet2017_4J,p3_PFDijet2017_4J,p4_PFDijet2017_4J,pa1_PFDijet2017_4J,pa2_PFDijet2017_4J,pa3_PFDijet2017_4J,pa4_PFDijet2017_4J,pmd1_PFDijet2017_4J,pmd2_PFDijet2017_4J,pmd3_PFDijet2017_4J,pmd4_PFDijet2017_4J,shapeBkg_PFDijet2017_4J_multi_PFDijet2017_4J__norm

combine -M AsymptoticLimits higgsCombine.scenario4.MultiDimFit.mH120.root --snapshotName MultiDimFit -t -1 --expectSignal 2 --saveToys -n .scenario4.expSig0p02pb.StatOnly --freezeParameters jer,jes,lumi,p1_PFDijet2017_4J_bin1,p2_PFDijet2017_4J_bin1,p3_PFDijet2017_4J_bin1,p4_PFDijet2017_4J_bin1,pa1_PFDijet2017_4J_bin1,pa2_PFDijet2017_4J_bin1,pa3_PFDijet2017_4J_bin1,pa4_PFDijet2017_4J_bin1,pmd1_PFDijet2017_4J_bin1,pmd2_PFDijet2017_4J_bin1,pmd3_PFDijet2017_4J_bin1,pmd4_PFDijet2017_4J_bin1,p1_PFDijet2017_4J_bin2,p2_PFDijet2017_4J_bin2,p3_PFDijet2017_4J_bin2,p4_PFDijet2017_4J_bin2,pa1_PFDijet2017_4J_bin2,pa2_PFDijet2017_4J_bin2,pa3_PFDijet2017_4J_bin2,pa4_PFDijet2017_4J_bin2,pmd1_PFDijet2017_4J_bin2,pmd2_PFDijet2017_4J_bin2,pmd3_PFDijet2017_4J_bin2,pmd4_PFDijet2017_4J_bin2,p1_PFDijet2017_4J_bin3,p2_PFDijet2017_4J_bin3,p3_PFDijet2017_4J_bin3,p4_PFDijet2017_4J_bin3,pa1_PFDijet2017_4J_bin3,pa2_PFDijet2017_4J_bin3,pa3_PFDijet2017_4J_bin3,pa4_PFDijet2017_4J_bin3,pmd1_PFDijet2017_4J_bin3,pmd2_PFDijet2017_4J_bin3,pmd3_PFDijet2017_4J_bin3,pmd4_PFDijet2017_4J_bin3,p1_PFDijet2017_4J_bin4,p2_PFDijet2017_4J_bin4,p3_PFDijet2017_4J_bin4,p4_PFDijet2017_4J_bin4,pa1_PFDijet2017_4J_bin4,pa2_PFDijet2017_4J_bin4,pa3_PFDijet2017_4J_bin4,pa4_PFDijet2017_4J_bin4,pmd1_PFDijet2017_4J_bin4,pmd2_PFDijet2017_4J_bin4,pmd3_PFDijet2017_4J_bin4,pmd4_PFDijet2017_4J_bin4,p1_PFDijet2017_4J_bin5,p2_PFDijet2017_4J_bin5,p3_PFDijet2017_4J_bin5,p4_PFDijet2017_4J_bin5,pa1_PFDijet2017_4J_bin5,pa2_PFDijet2017_4J_bin5,pa3_PFDijet2017_4J_bin5,pa4_PFDijet2017_4J_bin5,pmd1_PFDijet2017_4J_bin5,pmd2_PFDijet2017_4J_bin5,pmd3_PFDijet2017_4J_bin5,pmd4_PFDijet2017_4J_bin5,p1_PFDijet2017_4J_bin6,p2_PFDijet2017_4J_bin6,p3_PFDijet2017_4J_bin6,p4_PFDijet2017_4J_bin6,pa1_PFDijet2017_4J_bin6,pa2_PFDijet2017_4J_bin6,pa3_PFDijet2017_4J_bin6,pa4_PFDijet2017_4J_bin6,pmd1_PFDijet2017_4J_bin6,pmd2_PFDijet2017_4J_bin6,pmd3_PFDijet2017_4J_bin6,pmd4_PFDijet2017_4J_bin6,p1_PFDijet2017_4J_bin7,p2_PFDijet2017_4J_bin7,p3_PFDijet2017_4J_bin7,p4_PFDijet2017_4J_bin7,pa1_PFDijet2017_4J_bin7,pa2_PFDijet2017_4J_bin7,pa3_PFDijet2017_4J_bin7,pa4_PFDijet2017_4J_bin7,pmd1_PFDijet2017_4J_bin7,pmd2_PFDijet2017_4J_bin7,pmd3_PFDijet2017_4J_bin7,pmd4_PFDijet2017_4J_bin7,p1_PFDijet2017_4J_bin8,p2_PFDijet2017_4J_bin8,p3_PFDijet2017_4J_bin8,p4_PFDijet2017_4J_bin8,pa1_PFDijet2017_4J_bin8,pa2_PFDijet2017_4J_bin8,pa3_PFDijet2017_4J_bin8,pa4_PFDijet2017_4J_bin8,pmd1_PFDijet2017_4J_bin8,pmd2_PFDijet2017_4J_bin8,pmd3_PFDijet2017_4J_bin8,pmd4_PFDijet2017_4J_bin8,p1_PFDijet2017_4J_bin9,p2_PFDijet2017_4J_bin9,p3_PFDijet2017_4J_bin9,p4_PFDijet2017_4J_bin9,pa1_PFDijet2017_4J_bin9,pa2_PFDijet2017_4J_bin9,pa3_PFDijet2017_4J_bin9,pa4_PFDijet2017_4J_bin9,pmd1_PFDijet2017_4J_bin9,pmd2_PFDijet2017_4J_bin9,pmd3_PFDijet2017_4J_bin9,pmd4_PFDijet2017_4J_bin9,p1_PFDijet2017_4J_bin10,p2_PFDijet2017_4J_bin10,p3_PFDijet2017_4J_bin10,p4_PFDijet2017_4J_bin10,pa1_PFDijet2017_4J_bin10,pa2_PFDijet2017_4J_bin10,pa3_PFDijet2017_4J_bin10,pa4_PFDijet2017_4J_bin10,pmd1_PFDijet2017_4J_bin10,pmd2_PFDijet2017_4J_bin10,pmd3_PFDijet2017_4J_bin10,pmd4_PFDijet2017_4J_bin10,shapeBkg_PFDijet2017_4J_bin1_multi_ch1__norm,shapeBkg_PFDijet2017_4J_bin2_multi_ch2__norm,shapeBkg_PFDijet2017_4J_bin3_multi_ch3__norm,shapeBkg_PFDijet2017_4J_bin4_multi_ch4__norm,shapeBkg_PFDijet2017_4J_bin5_multi_ch5__norm,shapeBkg_PFDijet2017_4J_bin6_multi_ch6__norm,shapeBkg_PFDijet2017_4J_bin7_multi_ch7__norm,shapeBkg_PFDijet2017_4J_bin8_multi_ch8__norm,shapeBkg_PFDijet2017_4J_bin9_multi_ch9__norm,shapeBkg_PFDijet2017_4J_bin10_multi_ch10__norm


combine -M Significance higgsCombine.scenario1.MultiDimFit.mH120.root --snapshotName MultiDimFit -t -1 --expectSignal 2 --saveToys -n .scenario1.expSig0p02pb.StatOnly --freezeParameters jer,jes,lumi,p1_PFDijet2017_4J,p2_PFDijet2017_4J,p3_PFDijet2017_4J,p4_PFDijet2017_4J,pa1_PFDijet2017_4J,pa2_PFDijet2017_4J,pa3_PFDijet2017_4J,pa4_PFDijet2017_4J,pmd1_PFDijet2017_4J,pmd2_PFDijet2017_4J,pmd3_PFDijet2017_4J,pmd4_PFDijet2017_4J,shapeBkg_PFDijet2017_4J_multi_PFDijet2017_4J__norm

combine -M Significance higgsCombine.scenario4.MultiDimFit.mH120.root --snapshotName MultiDimFit -t -1 --expectSignal 2 --saveToys -n .scenario4.expSig0p02pb.StatOnly --freezeParameters jer,jes,lumi,p1_PFDijet2017_4J_bin1,p2_PFDijet2017_4J_bin1,p3_PFDijet2017_4J_bin1,p4_PFDijet2017_4J_bin1,pa1_PFDijet2017_4J_bin1,pa2_PFDijet2017_4J_bin1,pa3_PFDijet2017_4J_bin1,pa4_PFDijet2017_4J_bin1,pmd1_PFDijet2017_4J_bin1,pmd2_PFDijet2017_4J_bin1,pmd3_PFDijet2017_4J_bin1,pmd4_PFDijet2017_4J_bin1,p1_PFDijet2017_4J_bin2,p2_PFDijet2017_4J_bin2,p3_PFDijet2017_4J_bin2,p4_PFDijet2017_4J_bin2,pa1_PFDijet2017_4J_bin2,pa2_PFDijet2017_4J_bin2,pa3_PFDijet2017_4J_bin2,pa4_PFDijet2017_4J_bin2,pmd1_PFDijet2017_4J_bin2,pmd2_PFDijet2017_4J_bin2,pmd3_PFDijet2017_4J_bin2,pmd4_PFDijet2017_4J_bin2,p1_PFDijet2017_4J_bin3,p2_PFDijet2017_4J_bin3,p3_PFDijet2017_4J_bin3,p4_PFDijet2017_4J_bin3,pa1_PFDijet2017_4J_bin3,pa2_PFDijet2017_4J_bin3,pa3_PFDijet2017_4J_bin3,pa4_PFDijet2017_4J_bin3,pmd1_PFDijet2017_4J_bin3,pmd2_PFDijet2017_4J_bin3,pmd3_PFDijet2017_4J_bin3,pmd4_PFDijet2017_4J_bin3,p1_PFDijet2017_4J_bin4,p2_PFDijet2017_4J_bin4,p3_PFDijet2017_4J_bin4,p4_PFDijet2017_4J_bin4,pa1_PFDijet2017_4J_bin4,pa2_PFDijet2017_4J_bin4,pa3_PFDijet2017_4J_bin4,pa4_PFDijet2017_4J_bin4,pmd1_PFDijet2017_4J_bin4,pmd2_PFDijet2017_4J_bin4,pmd3_PFDijet2017_4J_bin4,pmd4_PFDijet2017_4J_bin4,p1_PFDijet2017_4J_bin5,p2_PFDijet2017_4J_bin5,p3_PFDijet2017_4J_bin5,p4_PFDijet2017_4J_bin5,pa1_PFDijet2017_4J_bin5,pa2_PFDijet2017_4J_bin5,pa3_PFDijet2017_4J_bin5,pa4_PFDijet2017_4J_bin5,pmd1_PFDijet2017_4J_bin5,pmd2_PFDijet2017_4J_bin5,pmd3_PFDijet2017_4J_bin5,pmd4_PFDijet2017_4J_bin5,p1_PFDijet2017_4J_bin6,p2_PFDijet2017_4J_bin6,p3_PFDijet2017_4J_bin6,p4_PFDijet2017_4J_bin6,pa1_PFDijet2017_4J_bin6,pa2_PFDijet2017_4J_bin6,pa3_PFDijet2017_4J_bin6,pa4_PFDijet2017_4J_bin6,pmd1_PFDijet2017_4J_bin6,pmd2_PFDijet2017_4J_bin6,pmd3_PFDijet2017_4J_bin6,pmd4_PFDijet2017_4J_bin6,p1_PFDijet2017_4J_bin7,p2_PFDijet2017_4J_bin7,p3_PFDijet2017_4J_bin7,p4_PFDijet2017_4J_bin7,pa1_PFDijet2017_4J_bin7,pa2_PFDijet2017_4J_bin7,pa3_PFDijet2017_4J_bin7,pa4_PFDijet2017_4J_bin7,pmd1_PFDijet2017_4J_bin7,pmd2_PFDijet2017_4J_bin7,pmd3_PFDijet2017_4J_bin7,pmd4_PFDijet2017_4J_bin7,p1_PFDijet2017_4J_bin8,p2_PFDijet2017_4J_bin8,p3_PFDijet2017_4J_bin8,p4_PFDijet2017_4J_bin8,pa1_PFDijet2017_4J_bin8,pa2_PFDijet2017_4J_bin8,pa3_PFDijet2017_4J_bin8,pa4_PFDijet2017_4J_bin8,pmd1_PFDijet2017_4J_bin8,pmd2_PFDijet2017_4J_bin8,pmd3_PFDijet2017_4J_bin8,pmd4_PFDijet2017_4J_bin8,p1_PFDijet2017_4J_bin9,p2_PFDijet2017_4J_bin9,p3_PFDijet2017_4J_bin9,p4_PFDijet2017_4J_bin9,pa1_PFDijet2017_4J_bin9,pa2_PFDijet2017_4J_bin9,pa3_PFDijet2017_4J_bin9,pa4_PFDijet2017_4J_bin9,pmd1_PFDijet2017_4J_bin9,pmd2_PFDijet2017_4J_bin9,pmd3_PFDijet2017_4J_bin9,pmd4_PFDijet2017_4J_bin9,p1_PFDijet2017_4J_bin10,p2_PFDijet2017_4J_bin10,p3_PFDijet2017_4J_bin10,p4_PFDijet2017_4J_bin10,pa1_PFDijet2017_4J_bin10,pa2_PFDijet2017_4J_bin10,pa3_PFDijet2017_4J_bin10,pa4_PFDijet2017_4J_bin10,pmd1_PFDijet2017_4J_bin10,pmd2_PFDijet2017_4J_bin10,pmd3_PFDijet2017_4J_bin10,pmd4_PFDijet2017_4J_bin10,shapeBkg_PFDijet2017_4J_bin1_multi_ch1__norm,shapeBkg_PFDijet2017_4J_bin2_multi_ch2__norm,shapeBkg_PFDijet2017_4J_bin3_multi_ch3__norm,shapeBkg_PFDijet2017_4J_bin4_multi_ch4__norm,shapeBkg_PFDijet2017_4J_bin5_multi_ch5__norm,shapeBkg_PFDijet2017_4J_bin6_multi_ch6__norm,shapeBkg_PFDijet2017_4J_bin7_multi_ch7__norm,shapeBkg_PFDijet2017_4J_bin8_multi_ch8__norm,shapeBkg_PFDijet2017_4J_bin9_multi_ch9__norm,shapeBkg_PFDijet2017_4J_bin10_multi_ch10__norm



--freezeParameters p1_PFDijet2017_4J_bin1,p2_PFDijet2017_4J_bin1,p3_PFDijet2017_4J_bin1,p4_PFDijet2017_4J_bin1,pa1_PFDijet2017_4J_bin1,pa2_PFDijet2017_4J_bin1,pa3_PFDijet2017_4J_bin1,pa4_PFDijet2017_4J_bin1,pmd1_PFDijet2017_4J_bin1,pmd2_PFDijet2017_4J_bin1,pmd3_PFDijet2017_4J_bin1,pmd4_PFDijet2017_4J_bin1,p1_PFDijet2017_4J_bin2,p2_PFDijet2017_4J_bin2,p3_PFDijet2017_4J_bin2,p4_PFDijet2017_4J_bin2,pa1_PFDijet2017_4J_bin2,pa2_PFDijet2017_4J_bin2,pa3_PFDijet2017_4J_bin2,pa4_PFDijet2017_4J_bin2,pmd1_PFDijet2017_4J_bin2,pmd2_PFDijet2017_4J_bin2,pmd3_PFDijet2017_4J_bin2,pmd4_PFDijet2017_4J_bin2,p1_PFDijet2017_4J_bin3,p2_PFDijet2017_4J_bin3,p3_PFDijet2017_4J_bin3,p4_PFDijet2017_4J_bin3,pa1_PFDijet2017_4J_bin3,pa2_PFDijet2017_4J_bin3,pa3_PFDijet2017_4J_bin3,pa4_PFDijet2017_4J_bin3,pmd1_PFDijet2017_4J_bin3,pmd2_PFDijet2017_4J_bin3,pmd3_PFDijet2017_4J_bin3,pmd4_PFDijet2017_4J_bin3,p1_PFDijet2017_4J_bin4,p2_PFDijet2017_4J_bin4,p3_PFDijet2017_4J_bin4,p4_PFDijet2017_4J_bin4,pa1_PFDijet2017_4J_bin4,pa2_PFDijet2017_4J_bin4,pa3_PFDijet2017_4J_bin4,pa4_PFDijet2017_4J_bin4,pmd1_PFDijet2017_4J_bin4,pmd2_PFDijet2017_4J_bin4,pmd3_PFDijet2017_4J_bin4,pmd4_PFDijet2017_4J_bin4,p1_PFDijet2017_4J_bin5,p2_PFDijet2017_4J_bin5,p3_PFDijet2017_4J_bin5,p4_PFDijet2017_4J_bin5,pa1_PFDijet2017_4J_bin5,pa2_PFDijet2017_4J_bin5,pa3_PFDijet2017_4J_bin5,pa4_PFDijet2017_4J_bin5,pmd1_PFDijet2017_4J_bin5,pmd2_PFDijet2017_4J_bin5,pmd3_PFDijet2017_4J_bin5,pmd4_PFDijet2017_4J_bin5,p1_PFDijet2017_4J_bin6,p2_PFDijet2017_4J_bin6,p3_PFDijet2017_4J_bin6,p4_PFDijet2017_4J_bin6,pa1_PFDijet2017_4J_bin6,pa2_PFDijet2017_4J_bin6,pa3_PFDijet2017_4J_bin6,pa4_PFDijet2017_4J_bin6,pmd1_PFDijet2017_4J_bin6,pmd2_PFDijet2017_4J_bin6,pmd3_PFDijet2017_4J_bin6,pmd4_PFDijet2017_4J_bin6,p1_PFDijet2017_4J_bin7,p2_PFDijet2017_4J_bin7,p3_PFDijet2017_4J_bin7,p4_PFDijet2017_4J_bin7,pa1_PFDijet2017_4J_bin7,pa2_PFDijet2017_4J_bin7,pa3_PFDijet2017_4J_bin7,pa4_PFDijet2017_4J_bin7,pmd1_PFDijet2017_4J_bin7,pmd2_PFDijet2017_4J_bin7,pmd3_PFDijet2017_4J_bin7,pmd4_PFDijet2017_4J_bin7,p1_PFDijet2017_4J_bin8,p2_PFDijet2017_4J_bin8,p3_PFDijet2017_4J_bin8,p4_PFDijet2017_4J_bin8,pa1_PFDijet2017_4J_bin8,pa2_PFDijet2017_4J_bin8,pa3_PFDijet2017_4J_bin8,pa4_PFDijet2017_4J_bin8,pmd1_PFDijet2017_4J_bin8,pmd2_PFDijet2017_4J_bin8,pmd3_PFDijet2017_4J_bin8,pmd4_PFDijet2017_4J_bin8,p1_PFDijet2017_4J_bin9,p2_PFDijet2017_4J_bin9,p3_PFDijet2017_4J_bin9,p4_PFDijet2017_4J_bin9,pa1_PFDijet2017_4J_bin9,pa2_PFDijet2017_4J_bin9,pa3_PFDijet2017_4J_bin9,pa4_PFDijet2017_4J_bin9,pmd1_PFDijet2017_4J_bin9,pmd2_PFDijet2017_4J_bin9,pmd3_PFDijet2017_4J_bin9,pmd4_PFDijet2017_4J_bin9,p1_PFDijet2017_4J_bin10,p2_PFDijet2017_4J_bin10,p3_PFDijet2017_4J_bin10,p4_PFDijet2017_4J_bin10,pa1_PFDijet2017_4J_bin10,pa2_PFDijet2017_4J_bin10,pa3_PFDijet2017_4J_bin10,pa4_PFDijet2017_4J_bin10,pmd1_PFDijet2017_4J_bin10,pmd2_PFDijet2017_4J_bin10,pmd3_PFDijet2017_4J_bin10,pmd4_PFDijet2017_4J_bin10,shapeBkg_PFDijet2017_4J_bin1_multi_ch1__norm,shapeBkg_PFDijet2017_4J_bin2_multi_ch2__norm,shapeBkg_PFDijet2017_4J_bin3_multi_ch3__norm,shapeBkg_PFDijet2017_4J_bin4_multi_ch4__norm,shapeBkg_PFDijet2017_4J_bin5_multi_ch5__norm,shapeBkg_PFDijet2017_4J_bin6_multi_ch6__norm,shapeBkg_PFDijet2017_4J_bin7_multi_ch7__norm,shapeBkg_PFDijet2017_4J_bin8_multi_ch8__norm,shapeBkg_PFDijet2017_4J_bin9_multi_ch9__norm,shapeBkg_PFDijet2017_4J_bin10_multi_ch10__norm







