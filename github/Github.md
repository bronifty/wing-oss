### Git Log and Commits
- git log to check commit history
- git checkout commit-hash to return to a commit 
- get checkout branch-name to return HEAD to the last commit in the branch

```sh
git log
git checkout f3ac63ffc8336079dc0a732c948bffdb846c753f
git log # does not show cany commits except for the ones before this
git checkout main
git log # shows call commits (we are back on main at the last commit)
```

### Git Branch

```sh
git branch feat-branch
git branch
git checkout feat-branch
```

- once a new branch is created (as a copy of main), we can checkout that new branch and HEAD will point to that second-branch, which is on the same commit as main
	- git HEAD points to the commit indirectly via branch 
		- in case of detached HEAD state (HEAD points to an earlier commit) no changes will be persisted
```shell
$ git log
commit 052b88404b5a3d7a8f8f56b272128d2609e22cfe (HEAD -> second-branch, main)
Author: bronifty <bronifty@gmail.com>
Date:   Mon Oct 23 15:56:23 2023 -0400

    added second-commit.md file

commit 974983ad8c8d6143a564aa2e548f45a2d15bedd8
Author: bronifty <bronifty@gmail.com>
Date:   Sun Oct 22 18:10:40 2023 -0400

    added README.md
```

- git checkout -b branch-name
```sh
bronifty@ubuntu:~/codes/deleteme$ git checkout -b third-branch
Switched to a new branch 'third-branch'
bronifty@ubuntu:~/codes/deleteme$ git branch
  main
  second-branch
* third-branch
```

- git checkout main and merge third branch changes
```sh
git checkout main
bronifty@ubuntu:~/codes/deleteme$ git merge third-branch
Updating 974983a..4759134
Fast-forward
 second-file.md | 0
 third-file.md  | 0
 2 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 second-file.md
 create mode 100644 third-file.md
bronifty@ubuntu:~/codes/deleteme$ git log
commit 47591344b9b5e4bf2102e1f25da6f9bb97f60a06 (HEAD -> main, third-branch)
Author: bronifty <bronifty@gmail.com>
Date:   Mon Oct 23 16:45:12 2023 -0400

    added third-file.md

commit c01d749d88c4b774d30dbf739c408948c73d56f2 (second-branch)
Author: bronifty <bronifty@gmail.com>
Date:   Mon Oct 23 16:39:37 2023 -0400

    added second file.md

commit 974983ad8c8d6143a564aa2e548f45a2d15bedd8
Author: bronifty <bronifty@gmail.com>
Date:   Sun Oct 22 18:10:40 2023 -0400

    added README.md
```
- now main has cumulative changes of all branches since branch 3 had all cumulative changes
- if we checkout a specific commit, we will have a detached HEAD
```sh
bronifty@ubuntu:~/codes/deleteme$ git checkout c01d749d88c4b774d30dbf739c408948c73d56f2
Note: switching to 'c01d749d88c4b774d30dbf739c408948c73d56f2'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

Turn off this advice by setting config variable advice.detachedHead to false

HEAD is now at c01d749 added second file.md
bronifty@ubuntu:~/codes/deleteme$ git log
commit c01d749d88c4b774d30dbf739c408948c73d56f2 (HEAD, second-branch)
Author: bronifty <bronifty@gmail.com>
Date:   Mon Oct 23 16:39:37 2023 -0400

    added second file.md

commit 974983ad8c8d6143a564aa2e548f45a2d15bedd8
Author: bronifty <bronifty@gmail.com>
Date:   Sun Oct 22 18:10:40 2023 -0400

    added README.md
bronifty@ubuntu:~/codes/deleteme$ git checkout second-branch
Switched to branch 'second-branch'
bronifty@ubuntu:~/codes/deleteme$ git log
commit c01d749d88c4b774d30dbf739c408948c73d56f2 (HEAD -> second-branch)
Author: bronifty <bronifty@gmail.com>
Date:   Mon Oct 23 16:39:37 2023 -0400

    added second file.md

commit 974983ad8c8d6143a564aa2e548f45a2d15bedd8
Author: bronifty <bronifty@gmail.com>
Date:   Sun Oct 22 18:10:40 2023 -0400

    added README.md
```

- git branch when in detached HEAD state will show that
```sh
git branch
* (HEAD detached at c01d749)
  main
  second-branch
  third-branch
```

- switch command works similarly to checkout but only works on branches
- -c flag will create a new branch when switch to it
```sh
bronifty@ubuntu:~/codes/deleteme$ git switch third-branch
Previous HEAD position was c01d749 added second file.md
Switched to branch 'third-branch'
bronifty@ubuntu:~/codes/deleteme$ git switch -c fourth-branch
Switched to a new branch 'fourth-branch'
```

3 parts of a github project are: 
1 working directory (local filesystem)
2 staging area (added files to git tracking)
3 git repo (committed files)

- check tracked files
```sh
git ls-files
```

### Deleting Files
- if i delete a file and check git ls-files, it will show the deleted file, but if i run git status then it will show the file has been deleted in the working directory
```shell
bronifty@ubuntu:~/codes/deleteme$ rm third-file.md 
bronifty@ubuntu:~/codes/deleteme$ git ls-files
README.md
second-file.md
third-file.md
bronifty@ubuntu:~/codes/deleteme$ git status
On branch fourth-branch
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    third-file.md

no changes added to commit (use "git add" and/or "git commit -a")
```
- if i want to delete the branch, i can either commit the change with git commit -a or i can remove the branch from staging by running git rm third-file.md

```shell
bronifty@ubuntu:~/codes/deleteme$ git ls-files
README.md
second-file.md
third-file.md
bronifty@ubuntu:~/codes/deleteme$ rm third-file.md 
bronifty@ubuntu:~/codes/deleteme$ git ls-files
README.md
second-file.md
third-file.md
bronifty@ubuntu:~/codes/deleteme$ git status
On branch fourth-branch
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    third-file.md

no changes added to commit (use "git add" and/or "git commit -a")
bronifty@ubuntu:~/codes/deleteme$ git rm third-file.md
rm 'third-file.md'
bronifty@ubuntu:~/codes/deleteme$ git status
On branch fourth-branch
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        deleted:    third-file.md

bronifty@ubuntu:~/codes/deleteme$ git ls-files
README.md
second-file.md
bronifty@ubuntu:~/codes/deleteme$ git commit -m "deleted third-file.md"
[fourth-branch 43b7d0f] deleted third-file.md
 1 file changed, 0 insertions(+), 0 deletions(-)
 delete mode 100644 third-file.md
```

- we cleared files from our working directory; next we'll learn how to undo unstaged changes
- i updated the README.md file but did not stage it (add to git)
```sh
git status
On branch fourth-branch
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   README.md

no changes added to commit (use "git add" and/or "git commit -a")
```
- now i will checkout the current branch - or revert the HEAD to the last commit - before the change, thereby undoing my unstaged change to README
```sh
bronifty@ubuntu:~/codes/deleteme$ git checkout README.md 
Updated 1 path from the index
bronifty@ubuntu:~/codes/deleteme$ git status
On branch fourth-branch
nothing to commit, working tree clean
```
- if we want to rollback unstaged changes in the whole working directory we can use dot
	- i made changes to the README and second-file
```sh
bronifty@ubuntu:~/codes/deleteme$ git status
On branch fourth-branch
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   README.md
        modified:   second-file.md

no changes added to commit (use "git add" and/or "git commit -a")
bronifty@ubuntu:~/codes/deleteme$ git checkout .
Updated 2 paths from the index
bronifty@ubuntu:~/codes/deleteme$ git status
On branch fourth-branch
nothing to commit, working tree clean
```

- we can use the new keyword 'restore' to revert unstaged changes
```sh
bronifty@ubuntu:~/codes/deleteme$ git status
On branch fourth-branch
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   second-file.md

no changes added to commit (use "git add" and/or "git commit -a")
bronifty@ubuntu:~/codes/deleteme$ git restore second-file.md 
bronifty@ubuntu:~/codes/deleteme$ git status
On branch fourth-branch
nothing to commit, working tree clean
```

- removing unstaged changes from untracked files has a separate command - git clean
	- git clean -df means delete force
		- -dn means delete and note which will happen
	- git clean can be run with a specific file or blank
```sh
bronifty@ubuntu:~/codes/deleteme$ touch untracked-file.txt
bronifty@ubuntu:~/codes/deleteme$ git status
On branch fourth-branch
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        untracked-file.txt

nothing added to commit but untracked files present (use "git add" to track)
bronifty@ubuntu:~/codes/deleteme$ git clean -d untracked-file.txt 
fatal: clean.requireForce defaults to true and neither -i, -n, nor -f given; refusing to clean
bronifty@ubuntu:~/codes/deleteme$ git clean -dn untracked-file.txt 
Would remove untracked-file.txt
bronifty@ubuntu:~/codes/deleteme$ git clean -dn
Would remove untracked-file.txt
bronifty@ubuntu:~/codes/deleteme$ git clean -df
Removing untracked-file.txt
bronifty@ubuntu:~/codes/deleteme$ git status
On branch fourth-branch
nothing to commit, working tree clean
```

- next we will undo staged changes
	- with the old syntax it will require 2 commands
		- git reset filename - unstages file
			- bring the latest status in the HEAD to the staging area
		- git checkout filename - revert unstaged change
			- then checkout the initial state
	
```sh
git status
On branch fourth-branch
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   second-file.md

no changes added to commit (use "git add" and/or "git commit -a")
bronifty@ubuntu:~/codes/deleteme$ git add second-file.md 
bronifty@ubuntu:~/codes/deleteme$ git status
On branch fourth-branch
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   second-file.md
bronifty@ubuntu:~/codes/deleteme$ git reset second-file.md 
Unstaged changes after reset:
M       second-file.md
bronifty@ubuntu:~/codes/deleteme$ git status
On branch fourth-branch
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   second-file.md

no changes added to commit (use "git add" and/or "git commit -a")
bronifty@ubuntu:~/codes/deleteme$ git checkout second-file.md 
Updated 1 path from the index
```

- new syntax - git restore --staged file
```sh
bronifty@ubuntu:~/codes/deleteme$ git status
On branch fourth-branch
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   second-file.md

no changes added to commit (use "git add" and/or "git commit -a")
bronifty@ubuntu:~/codes/deleteme$ git add .
bronifty@ubuntu:~/codes/deleteme$ git status
On branch fourth-branch
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   second-file.md

bronifty@ubuntu:~/codes/deleteme$ git restore --staged second-file.md 
bronifty@ubuntu:~/codes/deleteme$ git status
On branch fourth-branch
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   second-file.md

no changes added to commit (use "git add" and/or "git commit -a")
bronifty@ubuntu:~/codes/deleteme$ git checkout second-file.md 
Updated 1 path from the index
```

- git reset and restore do the same thing in this context, but restore was created to be more explicit
- git reset has another purpose, which is to reset the HEAD to delete commits