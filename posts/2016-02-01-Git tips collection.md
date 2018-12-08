---
title: Git tips collection
date: "2016-02-01"
category: 'git'
tags:
    - git
---

In this post, I collect some quick tips about Git that I learn daily.

## 1. How do I know my commit/change in which released version?

```bash

git tag --contains <commit-hash>

```

- The above command will show list of tags which corresponds release version.

## 2. How do I know my commit/change in which branch? Or How does I know my commit/change is already merge yet?

```bash

git br -r --contains <commit-hash>

```

- The above command will list of branches which have your commit.

## 3. Squash multiple commits

You can use this command to squash commits into one.

```bash

git rebase -i HEAD~[number_of_commits]

# squash the last 2 commits
# ex: git rebase -i HEAD~2

```

On running this command, you are taken to an interactive interface listing the commits and asking you which ones to squash. Ideally, you pick the latest commit and squash the old ones. And then you are asked to provide a commit message to the new commit. You should run this command in your branch because this process essentially re-writes your commit history and **you should re-write your branch only**.
After squashing, maybe your local branch is behind the remote branch. You can use push with force option to override the remote branch of your local branch.

```bash

git push --force

```

Or

```bash

git push --force origin [your_branch_name]

```
