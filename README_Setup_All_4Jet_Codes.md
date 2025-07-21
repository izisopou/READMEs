Login to lxplus9 and create a folder:

```
mkdir PairedDijetAnalysis/
cd ParedDijetAnalysis/
```

Download CMSSW:

```
cmsrel CMSSW_14_1_0_pre4
cd CMSSW_14_1_0_pre4/src
cmsenv
```

1) Git clone DijetRootTreeMaker -> needed for running big trees/ntuples

```
git clone -b Run3_150X https://github.com/CMSDIJET/DijetRootTreeMaker.git CMSDIJET/DijetRootTreeMaker
scram b -j 8
```

2) Git clone DijetRootTreeAnalyzer -> needed for running reduced trees/ntuples

```
git clone -b PairedDijetAnalysis https://github.com/CMSDIJET/DijetRootTreeAnalyzer CMSDIJET/DijetRootTreeAnalyzer
scram b -j 8
```

3) Git clone HiggsAnalysis/CombinedLimit -> needed for running HiggsCombine package to perform fits

```
git clone https://github.com/cms-analysis/HiggsAnalysis-CombinedLimit.git HiggsAnalysis/CombinedLimit 
cd HiggsAnalysis/CombinedLimit
git fetch origin
git checkout v10.0.0
scramv1 b clean; scramv1 b
```

From `/afs/cern.ch/user/i/izisopou/public/Combine_Codes/fit_functions/` take `RooDijet5ParamBinPdf.cc`, `RooModDijet5ParamBinPdf.cc` and `RooAtlas5ParamBinPdf.cc` and copy them inside `HiggsAnalysis/CombinedLimit/src` in your setup.

From `/afs/cern.ch/user/i/izisopou/public/Combine_Codes/fit_functions/` take `RooDijet5ParamBinPdf.h`, `RooModDijet5ParamBinPdf.h` and `RooAtlas5ParamBinPdf.h` and copy them inside `HiggsAnalysis/CombinedLimit/interface`

In the `HiggsAnalysis/CombinedLimit/src` folder modify the following files:

```
--> classes.h       : add the following lines:  
                                                #include "HiggsAnalysis/CombinedLimit/interface/RooDijet5ParamBinPdf.h"
                                                #include "HiggsAnalysis/CombinedLimit/interface/RooAtlas5ParamBinPdf.h"
                                                #include "HiggsAnalysis/CombinedLimit/interface/RooModDijet5ParamBinPdf.h"
```

```
--> classes_def.xml : add the following lines:  	
                                                        <class name="RooDijet5ParamBinPdf" />
                                                        <class name="RooAtlas5ParamBinPdf" /> 
                                                        <class name="RooModDijet5ParamBinPdf" /> 
```

cd back to `$CMSSW_BASE/src/HiggsAnalysis/CombinedLimit` and make a clean build:

```
cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit 
scramv1 b clean; scramv1 b
```


4) Git clone CombineHarvester -> needed for running HiggsCombine package to perform fits

```
cd $CMSSW_BASE/src/
git clone https://github.com/cms-analysis/CombineHarvester.git CombineHarvester
cd CombineHarvester/
git fetch origin
git checkout v3.0.0-pre1
scramv1 b clean; scramv1 b
```

The following changes concern crab job submissions:

In `HiggsAnalysis/CombinedLimit/python/tool_base/crab.py`:

L.15    -> `config.JobType.psetName = os.environ["CMSSW_BASE"] + "/src/CombineHarvester/CombineTools/scripts/do_nothing_cfg.py"`
L.17-21 -> `config.JobType.inputFiles = [
    os.environ["CMSSW_BASE"] + "/src/CombineHarvester/CombineTools/scripts/FrameworkJobReport.xml",
    os.environ["CMSSW_BASE"] + "/src/CombineHarvester/CombineTools/scripts/copyRemoteWorkspace.sh",
    os.environ["CMSSW_BASE"] + "/bin/" + os.environ["SCRAM_ARCH"] + "/combine",
]`

In `HiggsAnalysis/CombinedLimit/python/tool_base/CombineToolBase.py`:
L.381 -> `do_nothing_script = open(os.environ["CMSSW_BASE"] + "/src/CombineHarvester/CombineTools/scripts/do_nothing_cfg.py", "w")`

Go back to `$CMSSW_BASE/src`/ and make a clean build:

```
scramv1 b clean; scramv1 b
```

