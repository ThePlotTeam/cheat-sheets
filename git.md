# **Git CheatSheet**

- Initialize an empty repository

```sh
git init 
```

- Connect a repository to a remote repo

```sh
git remote add origin <url>
```

- Undo `git add <file>`

```sh
git reset <file>
```

- Create a branch and start editing on it

```sh
git branch <name>
git checkout <name>
```

- Push local branch to remote repository

```sh
git push --set-upstream origin <branch>
```

- Delete a branch locally and remotely

```sh
git branch -d <name>
git push origin --delete <name>
```

- Restore a file which has not been committed yet

```sh
git checkout -- <file> # Restore to latest commit
git checkout <commit_hash> -- <file> # Restore to specific commit
```

- Merge commits

```sh
git rebase -i HEAD~n #n is the number of commits to merge
```
