## git Branch Mgmt
To list branches
```bash
# local only
git branch

# Remote branches
git branch -r

# local and remote
git branch -a
```

To create a new branch & checkout/switch to it
```bash
git checkout -b newBranchName
```

To switch branches
```bash
git switch branchName
```

To delete a branch
```bash
# delete local version
git branch -d branchName

# delete remote version
git push origin --delete branchName
```
