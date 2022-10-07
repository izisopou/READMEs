To push in a different branch:

git branch <name_of_new_branch>    -> creates the new branch
git checkout <name_of_new_branch>  -> switch to the new branch (it's like we do cd)
git add file1 file2
git commit -m "Add my new changes"
git remote add origin https://github.com/CMSDIJET/DijetRootTreeMaker.git
git push -u origin <name_of_new_branch>

