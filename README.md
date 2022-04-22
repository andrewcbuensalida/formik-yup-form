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
then
    git difftool origin/staging
    
////////////////////////////////////////////////////////////////
workflow is, make changes on feature branch, then when ready, on feature branch, do a 
    git pull origin staging
this is to make sure the changes made by other developers that are in remote staging get merged into your local feature branch.
git pull first does a git fetch which updates all the tracking branches with its corresponding remote branch, then (if it's just git pull) merges current branch with its tracking branch, but if the origin and remote branch is specified (eg. git pull origin staging) then it will merge that remote branch into the current(head) branch.


////////////////////////////////////////////////////////////////////
to make a pr from the cmd, checkout feature branch, then
    gh pr create --base <branch you want to merge into, probably staging>

OR 

have to set an upstream branch with -u for the feature branch with
    git push -u origin feature1

now can just do
    git push

then once the feature1 is in the remote repo, do a pull request into staging branch

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