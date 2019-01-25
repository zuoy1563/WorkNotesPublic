HEAD = latest commit

### Squashing commits into one

If modifying the same thing, do not push to the remote repo several times but just squash all the commits into one. Even if after you push to the repo, you can also reduce the commit times you could see from remote side by rebasing the branch.

After cd to git repo, use the following code, HEAD~4 means from HEAD to the 4th recent commit.
```
$ git rebase -i HEAD~4
$ git push origin BRANCH_NAME --force
```
If you are not happy about using the current commit message, just change that before pushing by using 
```
$ git commit --amend -m "MESSAGE"
```

Otherwise can do this via soft rebase:
>https://stackoverflow.com/questions/6884022/collapsing-a-group-of-commits-into-one-on-git

### Stashing
If you want to save your work temporarily, you can use stash. It's not commit but just save it.

On the branch you want to save your work, start with
```
$ git stash
```
To apply changes on the top of the branch
```
$ git stash apply
```

Don't forget to clean it once you don't want it anymore.
```
$ git stash clear
```

### Merging
##### Merging Strategies
###### Fast Foward:
# 
>https://geeks.uniplaces.com/mastering-branches-in-git-f20cb2d0c51f
https://mijingo.com/blog/why-you-shouldnt-use-git-fast-forward

### Greping
>https://git-scm.com/docs/git-grep

### Diff & Merge Tool
```
$ git difftool -t meld COMMIT#2..COMMIT#1 FILE
$ git mergetool
```
