# GIT SNIPPET

### Log changes:
```
git log --oneline
```

### Show all branch:
```
git branch
```

### Change branch name

   + Step 1: Rename branch in local:
```
git branch -m bugfix
```
   + Step 2: Delete branch in remote:
    
```
git push origin --delete bogfix
```

### Add change to committed
```
 git commit --amend -a
```

### Clear change in local branch
```
git checkout --file
git checkout -- .
```

### Remove untrack file
```
git clean -f -d
```

### Remove file in staging (committed)
```
git reset --file
```

### Remove commit
```
git reset --hard HEAD~~
```

### Resolve conflict
- Commit file change

- Pull code

- Resolve conflict

- Commit resolve conflict

- Push code
```
git commit -a -m "Message"
git pull
git add .
git commit 
git push
```

### Using git credential to store config
```
git config --global credential.helper "store"
```
https://stackoverflow.com/questions/45925964/how-to-use-git-credential-store-on-wsl-ubuntu-on-windows

More about git: https://backlog.com/git-tutorial/vn/stepup/stepup1_1.html
