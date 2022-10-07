Instructions for Interpolation Codes
************************************

cmsrel CMSSW_10_2_13
cd CMSSW_10_2_3/test
cmsenv

git clone git@github.com:CMSDIJET/DijetShapeInterpolator.git DijetShapeInterpolator
cd DijetShapeInterpolator

First you will have to create an input rootfile with histograms of the X distribution (X = dijet or fourjet mass / Resonance mass) for all simulated signals. There are a few examples of such files named "InputShapes_xxx.root" in the directory inputs/
The code to create those input rootfiles is the following:

/afs/cern.ch/user/i/izisopou/public/ForMarc/Interpolation/create_input_histos_for_interpolation.C

Then do:

./extractShapes.py -i InputShapes_xxx.root > inputs/input_shapes_xxx.py

You will find a slightly modified extractShapes.py code to use here:
/afs/cern.ch/user/i/izisopou/public/ForMarc/Interpolation/extractShapes.py

This will create a .py with the bin contents of the X distribution (normalized to 1) for all input (simulated) signals. There are a few examples of such .py files inside inputs/ to take a look.

If you want to test the interpolation using for example the shapes at 500 and 700 GeV to predict the 600 GeV, then in this produced .py you need to delete the lines that correspond to the 600 GeV signal or don't create an input histogram for 600 GeV at all with the .C code, just create the X distributions only for 500 and 700 GeV.

Finally, do:

./getResonanceShapes.py -i inputs/input_shapes_xxx.py -f qq --massrange 600 3000 100 -o output/ResonanceShapes_xxx.root

You will find a slightly modified getResonanceShapes.py code to use here:
/afs/cern.ch/user/i/izisopou/public/ForMarc/Interpolation/getResonanceShapes.py

This will create the ResonanceShapes_xxx.root file with histograms of all interpolated shapes from 600 GeV to 3000 GeV with 100 GeV step. It will use as input all simulated signals you have provided and will always pick the two signals nearest to the mass that is to be interpolated each time. The code as is produces the dijet mass distributions in the "standard dijet binning" (but the binning can easily be changed in getResonanceShapes.py). Add --fineBinning to the command above to create 1 GeV binned histograms instead, that are needed for combine.  
The code is slightly modified to also save the histograms with the interpolated X distribution (it is not needed anywhere, it is just useful for internal checks)

Plotting script to compare the simulated and interpolated shape in std dijet binning along with their ratio:
/afs/cern.ch/user/i/izisopou/public/ForMarc/Plot_Shapes_Comparison.C


