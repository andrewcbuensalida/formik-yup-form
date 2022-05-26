git stuff part 2

/////////////////////////////////////////////

after
git clone https://github.com/cherrypickerdevelopment/cherrypicker-web.git
git checkout staging
this will create a local staging branch
note that it's not
git checkout origin/staging

///////////////////////////////////////////
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

Actually, this might be better. If you're fairly certain there will be no conflict and you want to save time, skip the difftool and do this
git pull --rebase origin staging
git pull first does a git fetch which updates all the tracking branches with its corresponding remote branch, then (if it's just git pull) merges current(head) branch with its tracking branch, but if the origin and remote branch are specified (eg. git pull origin staging) then it will merge that remote branch into the current(head) branch.
Now you can do the pull request below

////////////////////////////////////////////////////////////////////
to make a pr from the cmd, checkout feature branch, then
gh pr create --base <branch you want to merge into, probably staging (not origin/staging)>
this will create the feature branch in github, then make a pull request to staging

OR

(first method is better)have to set an upstream branch with -u for the feature branch with
git push -u origin feature1
now can just do
git push
then once the feature1 is in the remote repo, do a pull request into staging branch

DONT DO REBASE AFTER PULL REQUEST!, just do push to update if you're still working on your feature branch after making the pull request

to list open pull requests,
gh pr list

while waiting for pr approval, can work on feature2 that was branched from feature1
git checkout -b feature2

if there needs to be changes in feature1, when you need to pause development on feature2, this will remove changes and save it to a stash
git stash
OR
git stash push -m 'name of stash'
then fix feature1, then to update pull request
git push

then when you want to get back the stash, check the stash with
git stash list
then, to recover the latest stash
git stash pop
OR to recover a specific stash
git stash pop 'stash@{n}'

Once pr is approved on gh, they can delete the remote feature1 branch on gh, you can delete local feature1 with
git branch -D feature1

////////////////////////////////////////////////////////////////////////////////
to see the freshest branches
git branch --sort=-committerdate -a

////////////////////////////////////////////////////////////////////////////////
when there is a divergence in two branches and you try to merge, even if it was in different files, it wont be a fast forward, it will be a recursive strategy, meaning it will create a merge commit.

////////////////////////////////////////////
there wont be a merge conflict as long as the change is two lines away, aka not neigboring line aka not adjacent line.
even if it gets shifted down, there wont be a conflict.

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

////////////////////////////////////////
when there's a rebase conflict, fix conflict then
git rebase --continue
or
git rebase --skip
to skip the commit
///////////////////////////////
when want to overwrite a remote with the local and there's a conflict,
git push -f

/////////////////////////////////////

when merging, current change is the one in head(currently looking at) and incoming change is the change you want to apply to the current. It is opposite for rebase.

//////////////////////////////////////////////
cherrypicker workflow
I think Malcolm wants to review my code before approving my pull request into staging, so for me do you think this is a good strategy?

1. make my local staging up-to-date with the remote staging,
   git checkout staging
   git pull origin staging
2. Then create a new branch with
   git checkout -b 123-bug-fix
3. Then when it's ready, to make sure there's no conflict,
   git pull --rebase origin staging
   3.a If there's conflict,
   git rebase --abort
   And then maybe we can do a quick video call to resolve it.
4. If there's no conflict, create a pr with
   gh pr create --base staging
   if pr was already created,
   git push
   or git push -f if there was a divergence/conflict
5. While waiting for approval, I can work on a different bug, so I create another branch from the first bug fix with
   git checkout -b 456-bug-fix
   Repeat steps 3 and 4 until no more bugs
   Hopefully the bugs are independent from each another so there's less conflict.
6. If there is a revision for 123-bug-fix, on 456
   git stash
   git checkout 123
   make changes
   git push
   git checkout 456
   git rebase 123 (could also be git pull --rebase origin staging if 123 pr has already been merged)
   if there's conflict
   resolve conflict
   git add .
   git rebase --continue
   this will continue with the rebase
   then repeat steps 3 and 4

///////////////////////////////////////

If a pr-child is successfully merged, and it is a descendant of pr-parent, then pr-parent will automatically be merged, unless there's a divergence, like pr-parent was edited before pr-child was merged.

/////////////////////////////////////////
can only revert pr merges on github, not commits

//////////////////////////////////
git push
means it will push the current branch you're in, example feature1 to its corresponding remote feature1 in origin.
if it's
git push heroku feature1
this will merge your local feature1 branch into the remote(heroku) repos feature1 branch.
if it's
git push heroku feature1:master
this will merge local feature1 to remote master.

/////////////////////////////////
if you do
git pull
with no arguments, the default is unpredictable.
it's best to always specify with
git pull <remote name or url> <branch name>
this will update all tracking branches, then merge the tracking branch that corresponds with the one you are on
to make sure, do
git remote -v

//////////////////////////////
git branch
is used to delete, modify, create branches
git checkout -b
is used to create then switch

/////////////////////////

if your local branch is behind the upstream branch, cant git push. have to pull first, then push.

//////////////////

when a pr has been approved already, then you try to push to it, it will update the remote branch but wont make a pull request.
//////////////////

to remove a remote
git remote rm <remote-name>

///////////////////////////////////
1
2
3
4
5
6
7
