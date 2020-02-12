+++
author = "Jayson Ryter"
categories = ["Git"]
tags = ["Git"]
date = "2020-02-11"
description = "Origin? Upstream? You name it."
linktitle = "Git Remote"
title = "Git Part II - Git Remote"
type = "post"
+++

# Git Remote

This command is pretty straight forward, however it's one of those commands I didn't understand right away. Once I realized what it did and how to use it, I started using it quite often in my daily git usage. 

If you have a cloned repo and execute `git remote`, you will be able to see the list of remote repos that your branch tracks. If you have a fresh cloned repo, you will have a remote called "origin". Origin is the name of the repo you just cloned from whatever https URL or ssh source the repo is from, and usually has a HEAD reference of "master" by default. If you want to see the URL that origin is tied to, then use the command:

 `git remote -v`. 
```
[jayson@RyterINC jenkins] git remote -v
origin	https://github.com/jenkinsci/jenkins (fetch)
origin	https://github.com/jenkinsci/jenkins (push)
```

In the above example, I cloned the Jenkins CI github repo and as you can see, the URL that origin is tied to is the main repo where I cloned it from. If I wanted to add a remote repo to track (let's say a fork) I can add that to my remotes.

`git remote add fork https://github.com/RyterINC/jenkins`

```
[jayson@RyterINC jenkins] git remote -vv
fork	https://github.com/RyterINC/jenkins (fetch)
fork	https://github.com/RyterINC/jenkins (push)
origin	https://github.com/jenkinsci/jenkins (fetch)
origin	https://github.com/jenkinsci/jenkins (push)
```

Now I'm able to push and pull from my remote repo called fork. Note that I could have named this remote anything, like downstream or upstream. There's no right or wrong answer, whatever works for you.

If I wanted, I could add a remote repo that would be the same repo, but maybe just a different protocol like SSH. I've been in a situation before
where https was working for our companies bitbucket server, but SSH did not. I setup a remote URL for https and I was able to push and pull using 
that remote:

`git remote add origin-http https://github.com/ryterinc/Test-git-repo.git`
```
[jayson@RyterINC Test-git-repo.git] git remote -vv
origin	ssh://git@github.com/ryterinc/Test-git-repo.git (fetch)
origin	ssh://git@github.com/ryterinc/Test-git-repo.git (push)
origin-https	https://github.com/ryterinc/Test-git-repo.git (fetch)
origin-https	https://github.com/ryterinc/Test-git-repo.git (push)
```

This is useful, but what if I wanted to see the refs for that remote URL? `git ls-remote` can view the refs that are associated. Afterall, git is just a list of hashes that are tied to names. We can see that each reference (HEAD, refs/heads/master, and refs/heads/test-branch) are all tied to the same commit which is the most recent commit of my repo for the master branch. 
```
[jayson@RyterINC Test-git-repo] git ls-remote
From ssh://git@github.com/ryterinc/Test-git-repo.git
6870360f406cc96d5567ceac4142123d4d7352b4	HEAD
6870360f406cc96d5567ceac4142123d4d7352b4	refs/heads/master
6870360f406cc96d5567ceac4142123d4d7352b4	refs/heads/test-branch
```
I generally don't use git-remote outside of these commands and options. Just like adding, we can remove, update and rename these remote references. There are certainly use cases that justify the other options you will find in the docs, but this should be enough for you to get rolling and modify the remote tracking for each of your repos. [You can check out the docs here.](https://git-scm.com/docs/git-remote)
