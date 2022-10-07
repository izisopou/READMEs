mkdir Protolab
cd Protolab/
git clone https://gitlab.cern.ch/cms-jetmet/JERCProtoLab.git
cd JERCProtoLab/
git remote rename origin central
git remote add gitlab https://gitlab.cern.ch/izisopou/JERCProtoLab.git
git pull (to sync main repo with local)
git push gitlab master (to push changes to forked repo so that the main and forked are synced)
git add file1 file2
git commit -m "Add my new changes"
git push gitlab master (to push changes to forked repo)

Do a PR so that the changes in the forked repo can be reviewed and merged to the main repo.
