Main repo == https://gitlab.cern.ch/cms-jetmet/JERCProtoLab.git (the official repo)
Forked repo == https://gitlab.cern.ch/izisopou/JERCProtoLab.git (the repo you have copied)
Local repo == the repo in your lxplus where you work


Go to the main github page and fork the repository. (fork repo == copy repo)

Then log in to LXPLUS.

mkdir Protolab
cd Protolab/
git clone https://gitlab.cern.ch/cms-jetmet/JERCProtoLab.git	(fetches the main repo)
cd JERCProtoLab/
git remote rename origin central
git remote add gitlab https://gitlab.cern.ch/izisopou/JERCProtoLab.git	(this is the link of the forked repo!!!)
git pull (to sync main repo with local, this brings all the changes of the main repo to your local one in LXPLUS)

After you make changes to your local repo in LXPLUS:

git add file1 file2
git commit -m "Add my new changes"
git push gitlab master (to push changes to your forked repo!!! don't just do 'git push' because that will try to push your changes to the main repo and not your forked one)

Now that your forked repo has all the changes you want, go to your forked repo in the github page and do a PR so that the changes in the forked repo can be reviewed and merged to the main repo. There is a button that says 'Pull requests' and 'New pull request'.



Note: The same procedure applies for github (I think), but there every time you do a git push or pull and will ask you for your username and password, the password should be a github token, instead of your actual password. To get a github token, go to the github site, click on your profile on the top right, click "settings", click "developer settings", click "personal access token->Tokens(classic)" and then click "generate new token" and choose which priviledges you want to have with this token and how long will it last.
