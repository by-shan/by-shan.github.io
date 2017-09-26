---
title: "Notes_Git"
date: 2017-09-26
categories:
- Notebook
tags:
- Git
thumbnailImagePosition: left
thumbnailImage: //git-scm.com/images/logo@2x.png
---

This Git Nootebook is kept for my self-learning about Git. Though Rstudio is much easier to use git function, sometimes you will find only the git command can rescue you.
<!--more-->


# Getting Started
## Setting up a repository
<https://www.atlassian.com/git/tutorials/setting-up-a-repository>
The high level points / process:
* Initializing a new Git repo
* Cloning an existing Git repo
* Committing a modified version of a file to the repo
* Configuring a Git repo for remote collaboration
* Common Git version control commands

### Initializing a new repository: git init
To create a new repo, you 'wll use `git init` command. Executing this command will create a new `.git` subdirectory in your current working directory. This will also create a new master branch.
```git
cd / path / existing / code   ## locate within an already existing repo
git init        ## versioning an existing project
```

### Cloning an existing repo: git clone
Obtain a local development clone from remote repo. Once a developer has obtained a working copy, all version contro operations are managed through their local repository.
```git
git clone <repo url>
git clone git@github.com/shan-boyao/by.shan.github.io.git
```

### Saving changes to the repo: git add & git commit
```git
cd / path / to / project
echo "hello world" >> CommitTest.txt
git add CommitTest.txt
git commit -m "added CommiTest.txt to the repo"
```
`git add` command adds a change in the working directory to the staging area. It tells Git that you want to include updates to a particular file in the next commit. However, git add doesn't really affect the repository in any significant way—changes are not actually recorded until you run `git commit`.
```git
git add <file>  ##Stage all changes in <file> for the next commit.
git add <dir>  ##Stage all changes in <dir>
git add -p  ##Begin interactive staging session
```
In conjunction with these commands, you'll also need `git status` to view the state of the working directory and the staging area.
The `git commit` command commits the staged snapshot to the project history. Committed snapshots can be thought of as “safe” versions of a project—Git will never change them unless you explicity ask it to. Along with` git add`, this is one of the most important Git commands
```git
git add .
git commit ##Commit the staged snapshot. This will launch a text editor prompting you for a commit message. 
git commit -m "<message>"
git commit -a ##Commit a snapshot of all changes in <dir>
```

## Repo-torepo collaboration: git push
It’s important to understand that Git’s idea of a “working copy” is very different from the working copy you get by checking out source code from an SVN repository. Unlike SVN, Git makes no distinction between the working copies and the central repository—they're all full-fledged Git repositories.
#### Bare vs. cloned repositories
If you used `git clone` in the previous "Initializing a new Repository" section to set up your local repository, your repository is already configured for remote collaboration. `git clone` will automatically configure your repo with a remote pointed to the Git URL you cloned it from. This means that once you make changes to a file and commit them, you can `git push` those changes to the remote repository.
If you used `git init` to make a fresh repo, you'll have no remote repo to push changes to. A common pattern when initializing a new repo is to go to a hosted Git service like Bitbucket and create a repo there. The service will provide a Git URL that you can then add to your local Git repository and `git push` to the hosted repo. Once you have created a remote repo with your service of choice you will need to update your local repo with a mapping. We discuss this process in the Configuration & Set Up guide below.
If you prefer to host your own remote repo, you'll need to set up a "Bare Repository." Both `git init` and `git clone` accept a `--bare` argument. The most common use case for bare repo is to create a remote central Git repository.

### Configuration & set up: git config
The first thing you’ll want to do after installing Git is tell it your name/email and customize some of the default settings. A typical initial configuration might look something like the following:
```git
git config --global user.name "SHAN Boyao"
git config --global user.email john@example.com
```

## Saving changes
### git add

### git commit
<https://www.atlassian.com/git/tutorials/saving-changes#git-commit>
### git stash
`git stash` temporarily shelves (or stashes) changes you've made to your working copy so you can work on something else, and then come back and re-apply them later on. Stashing is handy if you need to quickly switch context and work on something else, but you're mid-way through a code change and aren't quite ready to commit.
<https://www.atlassian.com/git/tutorials/git-stash>
### .gitignore
Git sees every file in your working copy as one of three things:
1.tracked - a file which has been previously staged or committed;
2.untracked - a file which has not been staged or committed; or
3.ignored - a file which Git has been explicitly told to ignore.

Ignored files are usually build artifacts and machine generated files that can be derived from your repository source or should otherwise not be committed. Some common examples are:
•dependency caches, such as the contents of /node_modules or /packages
•compiled code, such as .o, .pyc, and .class files
•build output directories, such as /bin, /out, or /target
•files generated at runtime, such as .log, .lock, or .tmp
•hidden system files, such as .DS_Store or Thumbs.db
•personal IDE config files, such as .idea/workspace.xml

Ignored files are tracked in a special file named .gitignore that is checked in at the root of your repository. There is no explicit git ignore command: instead the .gitignore file must be edited and committed by hand when you have new files that you wish to ignore. .gitignore files contain patterns that are matched against file names in your repository to determine whether or not they should be ignored.

See more:
<https://www.atlassian.com/git/tutorials/git-stash>

## Inspecting a repository
<https://www.atlassian.com/git/tutorials/inspecting-a-repository>

### git status
The `git status` command displays the state of the working directory and the staging area. It lets you see which changes have been staged, which haven’t, and which files aren’t being tracked by Git. Status output does *not* show you any information regarding the committed project history. For this, you need to use `git log`.

### git log
The `git log` command displays committed snapshots. It lets you list the project history, filter it, and search for specific changes. While git status lets you inspect the working directory and the staging area, `git log` only operates on the committed history.

## Undoing changes
<https://www.atlassian.com/git/tutorials/undoing-changes>

##Rewriting history
<https://www.atlassian.com/git/tutorials/rewriting-history>

# Collaborating
## Syncing
<https://www.atlassian.com/git/tutorials/syncing>
Git’s collaboration model gives every developer their own copy of the repository, complete with its own local history and branch structure. Users typically need to share a series of commits rather than a single changeset. Instead of committing a changeset from a working copy to the central repository, Git lets you share entire branches between repositories.

### git remote
The `git remote` command lets you create, view, and delete connections to other repositories. Remote connections are more like bookmarks rather than direct links into other repositories. Instead of providing real-time access to another repository, they serve as convenient names that can be used to reference a not-so-convenient URL.
```git
git remote  ##List remote connections
git remote -v  ##Same as above, include URL of each connection
git remote add <name> <url>  ##create a new connection to a remote repository
git remote rm <name>  ##remove connection <name>
git remote rename <old-name> <new-name>  ##rename
```

### git fetch
The  `git fetch` command imports commits from a remote repository into your local repo. The resulting commits are stored as remote branches instead of the normal local branches that we’ve been working with. This gives you a chance to review changes before integrating them into your copy of the project.

### git pull
Merging upstream changes into your local repository is a common task in Git-based collaboration workflows. We already know how to do this with `git fetch` followed by `git merge`, but `git pull` rolls this into a single command.

###git push
Pushing is how you transfer commits from your local repository to a remote repo. It's the counterpart to `git fetch`, but whereas fetching imports commits to local branches, pushing exports commits to remote branches. This has the potential to overwrite changes, so you need to be careful how you use it. These issues are discussed below.
```git
git push <remote> <branch>
git push <remote> --force
git push <remote> --all  ##push all local branches to specified remote
```

## Making a Pull Request
<https://www.atlassian.com/git/tutorials/making-a-pull-request>

### How it works
1. A developer creates the feature in a dedicated branch in their local repo.
2. The developer pushes the branch to a public Github repository.
3. The developer files a pull request via Github.
4. The rest of the team reviews the code, discusses it, and alters it.
5. The project maintainer merges the feature into the official repository and closes the pull request. 

## Using branches
<https://www.atlassian.com/git/tutorials/using-branches>
### git branch

### git checkout
The `git checkout` command lets you navigate between the branches created by `git branch`. Checking out a branch updates the files in the working directory to match the version stored in that branch, and it tells Git to record all new commits on that branch. Think of it as a way to select which line of development you’re working on.
```git
git checkout <existing-branch>
git chekout -b <new-branch>
```

### git merge
Merging is Git's way of putting a forked history back together again. The `git merge` command lets you take the independent lines of development created by git branch and integrate them into a single branch.

Note that all of the commands presented below merge into the current branch. The current branch will be updated to reflect the merge, but the target branch will be completely unaffected. Again, this means that `git merge` is often used in conjunction with `git checkout` for selecting the current branch and `git branch -d` for deleting the obsolete target branch.

## Comparing workflows
<https://www.atlassian.com/git/tutorials/comparing-workflows>

# Migrating to Git
##SVN to Git
<https://www.atlassian.com/git/tutorials/svn-to-git-prepping-your-team-migration>

## ...

# Advanced Tips

<End>
