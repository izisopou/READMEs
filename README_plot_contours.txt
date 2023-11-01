Create a TH2D, named 'h_M4J_vs_M2J' with the four-jet vs average dijet mass and normalize it so that it has an integral of 1. Then save it to the 'M4J_vs_M2J_RunIIFall17_Suu-Diquark_W-0p0043_S-8400_chi-2100.root' file.

In the root terminal do:  

TFile f("M4J_vs_M2J_RunIIFall17_Suu-Diquark_W-0p0043_S-8400_chi-2100.root")
h_M4J_vs_M2J->Draw("colz");
gStyle->SetPaintTextFormat("4.3f"); 
h_M4J_vs_M2J->Integral() //should give 1

Find the minimum value 'MIN' for which if you add the bin contents of all bins with BinContent>=MIN then you get 0.68 or 0.95, for the 68% and 95% contour respectively.

h_M4J_vs_M2J->SetMinimum(MIN) //eg 0.00927

Set as maximum value something slightly largern than the 'MIN' value so that when you draw with the colz option you only see one color.
  
h_M4J_vs_M2J->SetMaximum(MIN+ε) //eg 0.0093
h_M4J_vs_M2J->Draw("cont1")
h_M4J_vs_M2J->SetLineColor(1)
h_M4J_vs_M2J->SetLineWidth(1)
h_M4J_vs_M2J->SetStats(0)
h_M4J_vs_M2J->Draw("cont3") 

Right-click on the plot and select SetLogZ. You will see some additional nonsensical lines, but ignore them. Right-click inside the panel but outside of these lines and select smooth. Then right-click and un-select the SetLogZ option. Finally, zoom in the plot as much as possible so that the contour occupies all the panel.

TFile *fout = TFile("contour.root","recreate")
fout->cd()
h_M4J_vs_M2J->Write()

When plotting the contour there may still be a few random lines, which you can remove by editing the pdf plot here: https://www.ilovepdf.com/edit-pdf
These random lines are an artefact of the smoothening.
