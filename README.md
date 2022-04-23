https://www.roboleary.net/vscode/2020/09/15/vscode-git.html
to use vs code as a diff tool
add this to User/user/.gitconfig
    [core]
  editor = code --wait
[diff]
  tool = vscode
[difftool "vscode"]
  cmd = code --wait --diff $LOCAL $REMOTE
[merge]
  tool = vscode
[mergetool "vscode"]
  cmd = code --wait $MERGED

then in cmd, checkout branch, then to check the differences of what other developers have done to the staging branch
to update tracking branches with the remote branches,
    git fetch
then on local staging branch
    git difftool origin/staging
    
////////////////////////////////////////////////////////////////
workflow is, clone repo, checkout staging, checkout a new feature branch from staging, make changes on feature branch, then when ready to push, on feature branch, do a 
    git fetch
to update tracking branches with remote branches, then 
    git difftool origin/staging
to see the diff of what others have done to make sure there's no conflict, then 
    git rebase origin/staging
this is to make sure the changes made by other developers that are in remote staging get merged into your local feature branch, but without the extra merge commit because it's rebase, not merge. if there still is a conflict, 
    git rebase --abort

If you're fairly certain there will be no conflict and you want to save time, skip the difftool and do this
    git pull --rebase origin staging
git pull first does a git fetch which updates all the tracking branches with its corresponding remote branch, then (if it's just git pull) merges current(head) branch with its tracking branch, but if the origin and remote branch are specified (eg. git pull origin staging) then it will merge that remote branch into the current(head) branch.
Now you can do the pull request below

////////////////////////////////////////////////////////////////////
to make a pr from the cmd, checkout feature branch, then
    gh pr create --base <branch you want to merge into, probably staging>
this will create the feature branch in github, then make a pull request to staging

OR 

have to set an upstream branch with -u for the feature branch with
    git push -u origin feature1

now can just do
    git push

then once the feature1 is in the remote repo, do a pull request into staging branch

DONT DO REBASE AFTER PULL REQUEST, just do merge to update if you're still working on your feature branch after making the pull request

to list open pull requests, 
    gh pr list
////////////////////////////////////////////////////////////////////////////////
to see the freshest branches
    git branch --sort=-committerdate -a


////////////////////////////////////////////////////////////////////////////////
when there is a divergence in two branches and you try to merge, even if it was in different files, it wont be a fast forward, it will be a recursive strategy, meaning it will create a merge commit.

////////////////////////////////////////////
there wont be a merge conflict as long as the change is two lines away, aka not neigboring line aka not adjacent line.

/////////////////////////////

if there's a conflict, do
    git merge --abort
/////////////////////////////////////

if there's an unstaged change on feature, then merge master, it'll say it's already up to date, as long as master didnt change.

////////////////////////////////////////

if you stage a change, then make another change in the same file, it wont be staged, and therefore wont be included in the commit. you would have to do git add . again to include it.


////////////////////////////////////////
merging takes the changes of the branch that you choose into the current branch that you're on.

////////////////////////////////////////////

checking out a tracking branch like origin/feature-x will create a local branch of the name feature-x if it doesnt exist already


///////////////////////////////////////
if you try to merge on a newer commit with an older commit or a commit on a different branch that doesnt diverge, itll say it's already up to date. but if it diverges, itll try to merge.
if you try to merge on an older commit that doesnt diverge, with a new commit, itll move the head to the newer commit but stay on that commit. weird.


/////////////////////////////////////////////
if you make changes while detached, even if you commit it, it will be lost. have to create a branch.


/////////////////////////////////////////////
pull request merges create an extra commit, but pushing directly doesn't.

////////////////////////////////////////////
if you try to push to a remote that has been changed, it wont let you, even if it's a different file. have to pull first, then push.

