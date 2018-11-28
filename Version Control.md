HEAD = latest commit

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

