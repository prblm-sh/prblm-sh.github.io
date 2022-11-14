## git (Global Information Tracker)
Version Control

[git cheatsheat](https://ndpsoftware.com/git-cheatsheet.html)
[github git cheatsheat](https://training.github.com/downloads/github-git-cheat-sheet/)

### Useful Commands
#### Branch Management
![gitBranches]({% link notes/gitBranches.md%})

#### Checkout/pull a sub-directory from a git repository.
Create a new empty directory, then run the following commands
Initiate the dir as a git repo
`git init`

Setup the git repo to only work with the *ROOT* directory of the repo (readme.md, .gitignore files, etc)
`git sparse-checkout init --cone`

Set the directories to pull from the git repo
`git sparse-checkout set nameOfDirToPull`
Multiple directories can be specified, do not use the full path of the directory. i.e, if the folder you want is `mainProjectFolder/tests`, the command would be `git sparse-checkout set tests`

Pull the subdirectories from the repo
`git pull https://github.com/userName/repoName.git`
NOTE: you must use `git pull` for this to work. Using `git clone` will clone the entire directory.

#git #versioncontrol