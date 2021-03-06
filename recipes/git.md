# Git

## Undoing things
### Undo uncommitted changes to a single file
```bash
git checkout -- <file>
```
### Undo commit
To just undo a local commit:
```bash
# just move the head pointer
git reset HEAD~1
# move the head pointer _and_ destroy the commit
git reset --hard HEAD~1
```

If you just need to make a small change to the existing commit, you can amend it instead of redoing it completely.
```bash
# make changes
git add stuff/you/changed.txt
git commit --amend
```

## Changing history
### Rename a branch
To rename a local branch:
```bash
# no need to specify the old branch if you have it checked out
git branch -m new-branch-name
```

If you already pushed the branch, you need to delete it from the remote.
After the old branch is gone, push the new one and make sure the local branch tracks it.
```bash
# delete branch on remote
git push origin :old-branch-name
# untrack the now gone remote branch
# it may not be needed to do this explicitly
git branch --unset-upstream
# push the new branch (with -u to set up tracking)
git push -u origin new-branch-name
```

## Tags
### Push commits _and_ tags in one go
`git push` only pushes changes, while `git push --tags` only pushes tags. `git push --follow-tags` only pushes annotated tags.
To push both:
```bash
git push --atomic origin <branch> <tag> [...]
```
Any number of branches and tags can be pushed. `--atomic` ensures that the command will not partially succeed if one of the pushed references fails.
