# git aliases


## `.gitconfig`
```
;Logging
la   = log --graph --decorate --pretty=oneline --abbrev-commit --all
laa  = log --graph  --pretty=format:'%C(yellow)%h%Creset -%C(cyan)%d %Creset%s %C(bold blue)<%an>%Creset (%cr)' --abbrev-commit --all
peeps = "!git log --pretty=format:%aN | sort | uniq -c | sort -rn"

;Basic
m   = merge --no-ff
rb  = rebase --preserve-merges
rbm  = rebase --preserve-merges master
st  = status -s
b   = branch -v
co  = checkout
com = checkout master
up  = pull --rebase

;Helpers
cmds = "!git config -l | grep alias | cut -c 7-"
branchname = rev-parse --abbrev-ref HEAD
repo = "!f(){ giturl=$(git config --get remote.origin.url); gu=${giturl/git@github.com:/https://github.com/}; start $gu; }; f"
removenm = "!git rm -r --cached node_modules && git commit -m 'Removing node_modules from history'"

;Branches
new = "!f(){ git checkout -b $1;};f"
remove   = "!f(){ git branch -D $1; git push origin :$1;}; f"
publish  = "!git push -u origin $(git branchname)"
overhere = "!f(){ git branch -f $1 && git co $1;}; f"
flush    = "!git reset --hard"
sync     = "!f(){ git fetch --all;git co $1/master;git branch -f master;git push origin master;git co -;}; f"
sub      = "!git submodule init; git submodule update"
clear    = "!git co master && git branch --merged | grep -v '\\*' | xargs -n 1 git branch -d"

;Commits
c      = "!f() { git status -s && git add -A && git commit -m \"$1\";}; f"
oops   = "!git add -A && git commit --amend -C HEAD"

;Rebasing
fixed      = "!git add -A && git rebase --continue"
jetpack    = "!git rebase --abort"
```



### logging
* `git la` - Lists out a tree-based history of the project
* `git laa` - Lists out a more detailed tree-like view, showing when each commit was donea nd the author
* `git peeps` - Lists out each contributor and the number of commits they have made

### basic
* `git m` - No-fast forward merge
* `git rb` - Rebase with preserved merge commits
* `git rbm` - Rebase with preserved-merge commits onto master
* `git st` - a Short status
* `git b` - Lists all branches
* `git co` - Checkout
* `git com` - Checkout master
* `git up` - Updates the current branch via a rebase


### helpers
* `git cmds` - Lists all your git aliases in the term
* `git branchname` - Returns the current branch name (used in other commands)
* `git repo` - Opens the github repo page in your web browser
* `git removenm` - Removes all node_modules from your git history

### branches
* `git new [branchname]` - Creates a new banch and checks it out
* `git remove [branchname]` - Removes a branch both locally and on your origin
* `git publish [branchname]` - Pushes the branch to origin and if it didn't exist previously, starts tracking it
* `git overhere [branchname]` - Brings the target branch over to your current commit HEAD and checks it out
* `git flush` - Reset hard
* `git sync [remotename]` - Fetches and updates your local and origin master with the remote's master (Useful for forks)
* `git sub` - Initializes and updates all submodules
* `git clear` - Removes all merged branches

### commits
* `git c [msg]` - Stages all changes, adds them, and commits with msg
* `git oops` - Adds current changes to the last commit (don't do this if you;ve pushed)

### rebasing
* `git fixed` - Adds all changes to the rebase and continues it
* `git jetpack` - Aborts from a rebase

