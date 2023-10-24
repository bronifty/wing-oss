[maximilian schwartzmueller github actions](https://www.udemy.com/course/github-actions-the-complete-guide/learn/lecture/34122306#learning-tools)

- HEAD points at latest commit 
- log older changes than what is checked out
```sh
git log
```
- checkout an earlier commit
```sh
git checkout <commit-hash> 
```
- go back to the branch with all the commits
```sh
git checkout main
# HEAD -> main
```
- revert a commit 
	- creates a new commit that undoes the actions of a specific commit (it's an 'undo' commit)
```sh
git revert <commit-hash>
```
- delete a commit 
	- it will remove all commits after a specific commit (returns you to a commit history before that commit and only the history preceding it)
```sh
git reset --hard <commit-hash>
```
- upload and downlaod  code 
```sh
git push & pull
```

### Branches
- create a branch and switch to it
```sh
git checkout -b feature-branch
```
- create a branch
```sh
git branch feature-branch
```
- list branches
```sh
git branch
```
- switch to a branch
```sh
git checkout feature-branch
```

> - git checkout can be used with a commit hash on a branch or a branch name, which is the HEAD of a series of commits
> - a branch will have all the commits of the original branch (eg switch to dev from main, dev includes the prior commits of main from which dev branched) 

- delete a branch
```sh
git branch -D feature-branch
```
- create and checkout branch
```sh
git checkout -b feature-branch
```
- make changes on feature branch and merge into main locally
	- checkout the branch which will have the changes merged into it
	- requires a commit message (default provided can accept)
```sh
git checkout main
git merge feature-branch # requires a commit message because it is a new commit
```

### Remote Branches
- link
```sh
git remote add origin <url>
git branch -M main
```
- pull
```sh
git pull origin main
```
- push
```sh
git push -u origin main
```
- in order to get permissions to push we need to (either set an ssh key or...) get a personal access token and set the url to include the username
	- https : // username@repo-url
```sh
git remote set-url origin https://bronifty@github.com/bronifty/github-actions-maximilian.git
```
> [git-scm credentials](https://git-scm.com/docs/gitcredentials)

### Pull Request
- if it's a simple change you can simply append the change from another branch without resolving any merge conflicts
```sh
# push the feature update
git push feat 
# switch back to main and merge the feature
git checkout main
git merge feat
# push to main (appends feature change without conflict)
git push main
```

### Git Config
```sh
git config --global user.name "bronifty"
git config --global user.email "bronifty@gmail.com"
```


```shell
git checkout spoild
git diff main..spoild > spoild.patch
git checkout main
git apply spoild.patch
```