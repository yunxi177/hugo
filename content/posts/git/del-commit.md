+++
title = "git 删除 commit 信息"
date = 2019-10-28T20:38:00+08:00
tags = ["git"]
categories = ["git"]
commentId="git-del-commit"
+++

```
#Clone your git repo
git clone <repo url>;
#Entre your local repo
cd lestatzhang.github.io;
#Checkout
git checkout --orphan latest_branch;
#Add all the files
git add -A;
#Commit the changes
git commit -am "Reinitialize";
#Delete the branch
git branch -D master;
#Rename the current branch to master
git branch -m master;
#Finally, force update your repository
git push -f origin master;
```