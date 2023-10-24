Q: I have a forked repo which is behind the source how do i bring it up to speed without losing my changes?

A:You can update your forked repository without losing your changes by using git merge or git rebase. Here are the steps:  
  
1. First, you need to fetch the changes from the original repository. You can do this by adding the original repository as a remote:  

```sh
git remote add upstream <original_repo_URL>
```

2. Fetch the changes from the original repository:  

```sh
git fetch upstream
```

3. Now, you have two options:  
  
a) Merge the changes: This will create a new commit in your local repository that includes the changes from the original repository.  
```sh
 git merge upstream/main
```
  
b) Rebase your changes: This will move your changes to the top of the changes from the original repository. It's like saying "first apply the changes from the original repository, then apply my changes".  
```sh
  git rebase upstream/main
```
 
4. Finally, push your changes to your forked repository:  
```sh
git push origin main
```

Remember to replace main with the branch name if you're not working on the main branch.  
Please note that if there are conflicts between your changes and the changes from the original repository, you'll need to resolve those conflicts manually.

