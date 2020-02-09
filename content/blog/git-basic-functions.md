+++
author = "Jayson Ryter"
categories = ["Git"]
tags = ["Git"]
date = "2020-02-08"
description = "Just enough Git to get you by"
linktitle = ""
title = "Git Basics Part I - Git Clone"
type = "post"
+++

# Just Technical Enough

Git is a tool most devs, Devops, SRE's, Network Engineers, DBA's, and basically anyone who works in modern software companies these days use.
Some people use it through a UI, some through an IDE, and some use the CLI (my personal use case). Git at first is rather confusing, especially if you come 
from a central server repo world like Subversion. At any rate, learning how to use the basic commands of git (and a few extra) will make your repo management
experience much easier. Learning through the CLI will be the most beneficial, as you won't have to be dependent on any specific UI or IDE as the CLI is
light weight and can be installed on most linux OS's. In this multi guide, we will be talking about the following commands:
```
git clone
git remote
git pull
git branch
git status
git rebase
git push
git commit
git checkout
git log
git fetch
```
These are the commands I use almost on a daily, and understanding each one is essential. Let's dive in!

# Git Clone - The First Steps

If you look at the Git documentation (which you should do) they will make this a lot more confusing than it really is. In a simplified description,
`git clone` will make a copy of the target repository as a directory on your filesystem at the current path you are at. So, if you were going to clone a repo
called "clone-this-repo" while you were in your home directory, let's say `/Users/yourAwesomeUser/`. After the clone you would could then `cd` into
`/Users/yourAwesomeUser/clone-this-repo` as that directory would have the contents of the repo you cloned, as well as a directory called `.git`. The `.git` directry has information that git uses to keep track of commits, references, branches and the configuration file to name a few.  

### So How Do You Actually Clone A Repo? 

`git clone` takes a repository as an argument and it's important to note that you can clone through two different protocols, either `ssh` or `https`. Most people
learn by just using the `https` method, but eventually you will want to learn to do this through `ssh` instead which I encourage you to do.
I have a public repo which you can clone to see the differences:

`git clone https://github.com/RyterINC/Test-git-repo`

`git clone ssh://git@github.com/RyterINC/Test-git-repo.git`

As you can see, there is not much difference between the syntax of the commands. The real difference here will be how you authenticate. When you clone through https, you have to authenticate via username and password, while SSH you would use an SSH key pair.

I won't teach you in this article on how to setup SSH through your repo or how to setup your git config (yet), but I can atleast tell you why I prefer SSH over https. SSH auth is just a time saver. It takes no time at all to add a key to github, and then you never have to type in a password again. Keys are easier to manage than passwords and ssh is consistent through all systems while https might take extra setup like plugins etc...I gaurentee once you get the hang of it you won't go back. 

https is of course though the easier method to just get things rolling at first. If I am on a temp system, I'm not going to bother setting up SSH keys if I just need to perform a one time clone (maybe for read only purposes). You will find what works for you. 

### What Else Can I do? 

`git clone` like many other git commands have additional arguments and options. Since you get the basic Idea of a clone, I will just demonstrate some use cases of those commands and briefly explain what they do so you can reference them. Take note however, I won't be covering everything, just some use cases that I think are more useful than others. If you want to see more options, [check out the docs](https://git-scm.com/docs/git-clone).

### Submodules

`git clone git@github.com/RyterINC/Test-git-repo.git --recurse-submodules`

If you cloned the repo from earlier, you might have noticed that in the repo there was an added submodule. Using the above command, you would also be able to clone the submodule that has been referenced. 

### Shallow clone

`git clone https://github.com/RyterINC/Test-git-repo --depth 2`

Only want a few commits worth? Shallow clone does just that. The history here is truncated to only the number of commits you specify. Useful for really big repos and maybe you want to save disk space. 

### clone branch

`git clone ssh://git@github.com/ryterinc/Test-git-repo.git --branch test-branch`

If you want to clone a specific branch, then this is your command. The remote repo's HEAD will be pointed to a specific HEAD reference. This command overwrites it and points your clone to the named branch. 

### bare

`git clone ssh://git@github.com/ryterinc/Test-git-repo.git --bare`

I've personally never used this option, but this will make your cloned directory a copy of <directory>/.git instead of a branch without remote tracking. This could be useful if you were just trying to get the refs and other git info associated info without actually cloning the project files itself. Each branch is copied as is, again, without tracking. 

### Mirror

`git clone ssh://git@github.com/ryterinc/Test-git-repo.git --mirror`

This gets you everything as far as refs, objects, remote tracking branches, and also implies bare which is why it's almost like `--bare` in the sense that you only get a .git directory. This is used in the event of migration, as it's supposed to be an exact mirror of the remote repo. 
