+++
author = "Jayson Ryter"
categories = ["Git"]
tags = ["Git"]
date = "2020-04-07"
description = "Can't Push Until You Pull"
linktitle = "Git Pull"
title = "Git Part III - Git Pull"
type = "post"
+++

# Git Pull

Making changes and pushing changes are all great, but what were to happen if you were making changes for a week and then when you try to merge, you find out 
that there are ten commits that got pushed to master? Welcome to conflict city. Being able to pull changes from a remote repository into yours is not only 
helpful it's a necessity. The scenario I used as an example is one that happens all the time, and can be avoided by just remembering to use `git pull` frequently or 
whenever there are changes that happen upstream. 

As you may have guessed already, `git pull` takes changes from another repo or local branch and pulls it into our working copy. 

Depending on where you work or what you're doing, there's not going to be a ton of different ways to use this command. 

`git pull origin master`

The command above is my go to usage for git pull. This command pull from my origin remote repo of whatever project I'm on, and pulls in changes from the master branch. 
If there are any changes in master it will be integrated into my branch and hopefully I have no conflicts, but it's always better to deal with conflicts earlier than
later after you have finished coding everything. 

There have been times in my ventures where I have had to pull from a co-workers branch. Maybe to test his changes to see how they will work with mine or vice versa. You
can pull from other branches much like you can pull from master. 

`git pull origin <branch-name>`

You can even pull from other remotes or forks besides origin.

Git pull is an abstraction. It's the same as doing a `git fetch` and then a `git merge`, but `git pull` is a shortcut to this chain of commands. 

#Anything else? 

I think a useful technique for git pull is the use of the `--rebase` option. As we said, `git pull` will perform a fetch and then a merge. Well, if you don't want
the disgusting merge commit you could run `git pull --rebase` to perform a rebase instead. We haven't gotten into rebase yet, but basically you are taking your 
commits and putting them on top of the remote repository you are integrating with.  
