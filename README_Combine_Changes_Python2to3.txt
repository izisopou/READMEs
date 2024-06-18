The newest HiggsCombine v10 version in CMSSW_14_1_0_pre4 is based on python3. We therefore had to make changes to our python scripts:

CMSDIJET/DijetRootTreeAnalyzer/python/BinnedFit_4jets.py
CMSDIJET/DijetRootTreeAnalyzer/python/WriteDataCard_4jets.py  (-> fit with only one function)
CMSDIJET/DijetRootTreeAnalyzer/python/WriteDatacard_4jets_envelope.py (-> fit with envelope using the dijet, atlas and moddijet functions)
CMSDIJET/DijetRootTreeAnalyzer/python/GetCombine_final.py
CMSDIJET/DijetRootTreeAnalyzer/python/Plot1DLimit_final.py
CMSDIJET/DijetRootTreeAnalyzer/python/Plot1DSignificance_final.py

The above scripts are related to the paired dijet search but they are basically the same for the dijet search. So the above can be used when running the dijet search as well (only some stylistics need to be changed).

The changes from python2 to python3 were the following:

1) Fix identation problems, i.e. do not mix spaces with tabs, use only spaces

2) Put parentheses in print and exec commands, eg. print 'hello world' becomes print ('hello world')

3) Delete the __init__.py files inside python/, python/framework/ and python/rootTools/ and modify the imports to:

   from rootTools.RootIterator import RootIterator
   from rootTools import Utils
   from framework import Config

   Due to that replace rootTools.Utils. -> Utils.

4) Change:
   
   for par in rootTools.RootIterator.RootIterator(parSet):
   
   to:
   
   iterator = RootIterator(parSet)
   for par in iterator:

5) In the BinnedFit_4jets.py change:

   for k, g in groupby(enumerate(sidebandBins), lambda (i,x):i-x):

   to:

   for k, g in groupby(enumerate(sidebandBins), lambda i,x:i-x):

6) The GetCombine_final.py, Plot1DLimit_final.py and Plot1DSignificance_final.py have addi










