log in to LXPLUS
export PATH=$HOME/.local/bin:/afs/cern.ch/cms/lumi/brilconda-1.1.7/bin:$PATH
pip uninstall brilws
pip install --install-option="--prefix=$HOME/.local" brilws
brilcalc lumi -i Cert_271036-284044_13TeV_Legacy2016_Collisions16_JSON_APV.txt -u /pb --normtag /cvmfs/cms-bril.cern.ch/cms-lumi-pog/Normtags/normtag_BRIL.json

Check the recorded lumi (not the delivered one).
