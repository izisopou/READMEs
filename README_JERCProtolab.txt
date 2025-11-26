Go to the gitlab page and fork the repository.

Then log in to LXPLUS.

git clone https://gitlab.cern.ch/izisopou/JERCProtoLab.git	(fetches the forked repo)
cd JERCProtoLab/						
git pull 							(to bring any changes from the forked repo to LXPLUS)
git branch							(to see in which branch we are right now)
git branch my_branch 						(creates a branch named "my_branch")
git checkout my_branch 					(cd to the new branch)
git add file1 file2
git commit -m "Add my new changes"
git push -u origin my_branch 					(pushes the changes to your forked)

Now that the branch "my_branch" of your forked repo has all the changes you want, go to your forked repo in the gitlab page and do a Merge Request (MR) so that the changes in the btanch of the forked repo can be reviewed and merged to the main repository. There is a button that says 'Pull requests' and 'New pull request'.
