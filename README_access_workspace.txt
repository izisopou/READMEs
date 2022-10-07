TFile *f1 = new TFile("output/13slices_Envelope_3_func_plots_alpha0p25/dijet_combine_Suu_Chi_8400_2100_lumi-137.000_PFDijet2017_4J_alphabin8.root")
f1 -> ls();
RooWorkspace *w1 = (RooWorkspace*) f -> Get("wPFDijet2017_4J_alphabin1")
w1 -> Print()
RooDataHist *hobs = (RooDataHist*) w1->data("data_obs")
RooDataHist *hnominal = (RooDataHist*) w1->data("PFDijet2017_4J_alphabin1_Suu")
RooDataHist *hjerup = (RooDataHist*) w1->data("PFDijet2017_4J_alphabin1_Suu_jerUp")
RooDataHist *hjerdown = (RooDataHist*) w1->data("PFDijet2017_4J_alphabin1_Suu_jerDown")

RooRealVar  *th1x = (RooRealVar*) w1->var("th1x")
RooPlot *frame = th1x->frame()
//hobs->plotOn(frame)


hnominal->plotOn(frame,RooFit::LineColor(kBlack),RooFit::MarkerColor(kBlack),RooFit::DrawOption("C E0"),RooFit::DataError(RooAbsData::None),RooFit::XErrorSize(0))
hjerup->plotOn(frame,RooFit::LineColor(kRed),RooFit::MarkerColor(kRed),RooFit::DrawOption("C E0"),RooFit::DataError(RooAbsData::None),RooFit::XErrorSize(0))
hjerdown->plotOn(frame,RooFit::LineColor(kGreen),RooFit::MarkerColor(kGreen),RooFit::DrawOption("C E0"),RooFit::DataError(RooAbsData::None),RooFit::XErrorSize(0))

frame->Draw()

DrawOption(“E0”),DataError(RooAbsData::None),XErrorSize(0)
