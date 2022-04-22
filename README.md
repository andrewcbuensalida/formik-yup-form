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

 then in cmd, checkout branch, then
    git difftool origin/
    
////////////////////////////////////////////////////////////////

to make a pr from the cmd, checkout feature branch, then
    gh pr create --base <branch you want to merge into>