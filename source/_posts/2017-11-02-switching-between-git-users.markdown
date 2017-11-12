---
layout: post
title: "switching between git users"
date: 2017-11-02 23:37:41 -0400
comments: true
categories: 
---

## Git Config

Something I had never thought about was exactly how git manages your user configurations.  Luckily I had just the issue that warranted this kind of knowledge!  I wanted to set up another github account which would host a new blog I would be hosting using github pages.  I didn't want to use my personal account because I'm already hosting this blog there ;)  Anyways I needed to figure out how to manage permissions for pushing to different remote repos between two separate github accounts.

<!--more-->

The way "I" needed to do this (YMMV unfortunately) was by activating a specific user in the `.git/config` file under my git repo.  When you init a git repo it creates a config file local to that repo.  Git also creates a global configuration file which stores other stuff.  I like to think of these as the git version of `nvm` ([node version manager](https://github.com/creationix/nvm)).  When I'm in different node repos I may want to use different versions of node, which is where nvm comes in.

Same principle for git.  When I am in different github repositories I may need to switch between accounts.  This may all sound very trivial but it's something I forgot at some point so I wanted to document it.  Anyways, after I uploaded my public ssh key to github, all I  needed to do was add the ssh host in `~/.ssh/config`

```bash
# githubWork
Host work
   HostName github.com
   User git
   IdentityFile ~/.ssh/my_work_account_rsa
``` 

I also think you need to add the below to the `~/.ssh/config` file but I'm not entirely sure.  I'll have to do some more investigating..

```bash
Host *
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/my_work_account_rsa
```

Then when I wanted to use this account for a git repo, I would initialize the repo and modify the `.git/config` file to use 

```bash
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
	ignorecase = true
	precomposeunicode = true
[remote "origin"]
	url = git@work:myWorkGitAccount/test.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[user]
	name = myWorkGitAccount
	email = my_work_email@gmail.com
[branch "master"]
	remote = origin
	merge = refs/heads/master
```

And presto I was able to push to my remote git repo using my "work" account ;)  I didn't have to do anything outside of this because the local `.git/config` file overrides the global file (and the global file overrides the system).  But yeah that's how it's done!  I don't know why I only needed this to manage two github accounts and not when I was setting up my bitbucket repo..  Not sure what happened there.  Some sort of magic maybe?  Eh I'm sure I'll have to figure that out eventually.  But for now this worked so I'm going to stick with this process for my future needs.
