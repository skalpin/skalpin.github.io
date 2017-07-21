---
layout: post
title:  "Use Git to Make Changes"
date: 2017-7-21
categories: update
---

# Use Git to Make Changes

I have been using `git` for a number of years and have come up with a workflow that lets me write code how I want, and then clean up the process later. My philosphy has always been
>Nobody wants to know how I write code. This is a mess!

## Creating a new feature

Generally on my projects we follow the *gitflow* process, but that still leaves me plenty of room to create my own process between `git checkout -b <featureBranch>` to creating a pull request in sourcecontrol. Here's an overview of my process to introduce changes while I'm working on a team. After selecting a feature to begin work on I'll create a feature branch. Then I commit a **lot** and I don't worry about what my commit messages are. Periodically I'll check to see if the development branch has been updated and if it has I'll rebase my changes on top. If my branch has many many commits, I may squash some changes before rebasing onto the develop branch to make conflicts easier to resolve. Then when my feature is complete, I'll do an interactive rebase onto the develop branch. The rebase allows me to reorder, group, squash, and edit commit messages until each commit is a complete *thought* and my history is a few commits ahead of develop. **Then** I'll submit a pull request and ask for my branch to be merged into develop. I also use several aliases to make this process easier on myself.

## Tools

First lets start with my tools.

1. Git for Windows (I'm using windows obviously)
1. Cmder (cmd shell)
1. Poshgit (powershell git tools)
1. Vim
1. kdiff3

### Aliases used

* `git config --global alias.nf "!f() { git checkout -b stevek/$1; }; f"`
* `git config --global alias.publish "!git push -u origin develop"`
* `git config --global alias.pushf push --force-with-lease`
* `git config --global alias.f fetch -p`
* `git config --global alias.lg log --color --graph --pretty=format:'%Cred%h%Creset %s%C(yellow)%d%Creset %Cgreen(%cr) %C(bold blue)<%an>%Creset'`
* `git config --global alias.in "!git lg ..origin/develop"`
* `git config --global alias.update "!git pull --rebase --autostash origin develop"`

## Have No Fear

Git does not delete commits (for something like a month). If you have accidently reset or rebased you can always see the commits you had checked out with `git reflog`. This log keeps track of where you have been and you can always `git reset --hard <commithash>` to move back to where you were. The only changes you may lose are the ones you didn't comimit.

## Creating my feature branch

Create your branch with
`git nf <itemnumber>-<feature>`. This will create and checkout a branch that looks something like *stevek/1122-myNewFeature*. The folder helps me keep track of **my** branches. Usually I own particular features and work on them myself. Then I generally like to push and set the upstream right away with `git publish`. 

## Making commits

Make commits freely. Do not working about building the code, do not worry about commit messages. This is the reason I love git. While I'm refactoring code I'm making commits that don't build and half solutions. Then if I need to go back at any point I can find the commit and run `git reset --hard <commithash>`. That will reset my branch back to the specified commit and remove all my changes. Again, the only changes you will lose are the ones you didnt' commit.

## Watching for Changes From Developers

You should fetch the remote repository frequently. I also like to prune at the same time so I use `git f`. Then you can check to see if you need to update your feature branch by looking at the incoming commits. Do this with `git in`. Then if there are incoming commits, you can decide what to do. When updating my branch I generally choose to rebase unless I am working with other developers in the branch.

## Cleaning Up History

Every once in a while I will realize that I've made several commits and either merging from develop or rebasing onto develop will probably be difficult. The reason is, you need to resolve conflicts for **each** commit. Find the commit to rebase onto with `git merge-base <yourbranch> develop`. Then clean up your history with `git rebase -i <mergebasehash>`. You only need to copy the first 6 characters or so of the hash.

The rebase command will open up your default editor (hopefully vim), and give you instructions on how to correct your commits. Save and close the file. Git will then follow your instructions / break / and open your editor to let you reword the commit messages you wanted to reword.

During the rebase if you have decided that you messed something up and want to abort run `git rebase --abort`. If instead the rebase worked well and you still want to undo it, take a look at the reflog `git reflog` and choose the previous commit hash. Then execute `git reset --hard <hash>`

Once complete you will notice that your remote branch and local have gotten out of sync. Execute `git pushf` to force the remote branch to match your local.

## Grab Changes From Develop

When you feel you are ready to get updates from develop run `git update`. This will rebase your branch against the most recent commit in the remote develop branch and let you make changes to your commits if you wish.

Again you will have to force your changes to the remote branch with `git pushf`.

## Repeat

Continuing on with your work you can repeate the process of rebasing / squashing / updating from develop until you feel the branch is ready and acceptable to be merged into develop.

## Create a Pull Request.

Each system is a little different, but generally in GitHub or VSTS you would now go find your branch and create a pull request using the web ui. Then your fellow developers will make comments about your code, make you feel like garbage, and eventually accept your request.

## Don't Stop!

While this process is happening **You don't need to wait!**. Grab another feature or bug, branch from the most recent commit in develop and start working! There is no reason to ever stop working while you are using git. (unless of course there is no work to do)