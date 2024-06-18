The newest HiggsCombine v10 version in CMSSW_14_1_0_pre4 is based on python3. We therefore had to make changes to our python scripts compared to the previous setup of HiggsCombine v8 in CMSSW_10_2_13:

CMSDIJET/DijetRootTreeAnalyzer/python/BinnedFit_4jets.py (->bkg fits)
CMSDIJET/DijetRootTreeAnalyzer/python/WriteDataCard_4jets.py  (-> signal+bkg fits with only one function)
CMSDIJET/DijetRootTreeAnalyzer/python/WriteDatacard_4jets_envelope.py (-> signal+bkg fits with envelope using the dijet, atlas and moddijet functions)
CMSDIJET/DijetRootTreeAnalyzer/python/GetCombine_final.py  (takes limit and signif combine root files creates inputs for plotting)
CMSDIJET/DijetRootTreeAnalyzer/python/Plot1DLimit_final.py
CMSDIJET/DijetRootTreeAnalyzer/python/Plot1DSignificance_final.py

The above scripts are used in the paired dijet search but they are basically the same for the dijet search. So the above can also be used when running the dijet search, and only some minor stylistics need to be changed.

We also had to modify two more python scripts in the framework:

CMSDIJET/DijetRootTreeAnalyzer/python/framework/Config.py
CMSDIJET/DijetRootTreeAnalyzer/python/rootTools/RootIterator.py

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

6) The GetCombine_final.py, Plot1DLimit_final.py and Plot1DSignificance_final.py have additional arguments about the signal width (just for naming purposes in the files) and plotting a mixture of asymptotic and hybridnew limits/significances.

7) Change:

   rt.AddressOf(s,"mass")

   to:

   rt.addressof(s,"mass")

8) Change:

   exec 'xsecULObs = xsecTree.xsecULObs_%s'%Box
   exec 'xsecULExp = xsecTree.xsecULExp_%s'%Box
   exec 'xsecULExpPlus = xsecTree.xsecULExpPlus_%s'%Box
   exec 'xsecULExpMinus = xsecTree.xsecULExpMinus_%s'%Box
   exec 'xsecULExpPlus2 = xsecTree.xsecULExpPlus2_%s'%Box
   exec 'xsecULExpMinus2 = xsecTree.xsecULExpMinus2_%s'%Box            
   xsecULObs = xsecULObs
   xsecULExp = xsecULExp                
   xsecULExpPlus = max(xsecULExpPlus,xsecULExp)
   xsecULExpMinus = min(xsecULExpMinus,xsecULExp)
   xsecULExpPlus2 = max(xsecULExpPlus2,xsecULExpPlus)
   xsecULExpMinus2 = min(xsecULExpMinus2,xsecULExpMinus)

   to:
   
   local_vars = {'xsecTree': xsecTree}     
   exec ('xsecULObs_t = xsecTree.xsecULObs_%s'%Box,globals(), local_vars)
   exec ('xsecULExp_t = xsecTree.xsecULExp_%s'%Box,globals(), local_vars)
   exec ('xsecULExpPlus_t = xsecTree.xsecULExpPlus_%s'%Box,globals(), local_vars)
   exec ('xsecULExpMinus_t = xsecTree.xsecULExpMinus_%s'%Box,globals(), local_vars)
   exec ('xsecULExpPlus2_t = xsecTree.xsecULExpPlus2_%s'%Box,globals(), local_vars)
   exec ('xsecULExpMinus2_t = xsecTree.xsecULExpMinus2_%s'%Box,globals(), local_vars)
   xsecULObs = local_vars['xsecULObs_t']
   xsecULExp = local_vars['xsecULExp_t']
   xsecULExpPlus = max(local_vars['xsecULExpPlus_t'],local_vars['xsecULExp_t'])
   xsecULExpMinus = min(local_vars['xsecULExpMinus_t'],local_vars['xsecULExp_t'])
   xsecULExpPlus2 = max(local_vars['xsecULExpPlus2_t'],local_vars['xsecULExpPlus_t'])
   xsecULExpMinus2 = min(local_vars['xsecULExpMinus2_t'],local_vars['xsecULExpMinus_t'])

9) In the Config.py script change:

   import ConfigParser, os
   self.config = ConfigParser.ConfigParser()

   to

   import configparser, os
   self.config = configparser.ConfigParser()

10) In the Config.py script change:

    if self.config.has_option(box, var) or self.config.defaults().has_key(var):

    to:

    if self.config.has_option(box, var) or var in self.config.defaults():

11) In the RootIterator.py script change:

    def next(self):

    to:

    def __next__(self):
