---
layout: post
title:  "Installing nvm in WSL with fish and omf"
date:   2019-06-21 19:23:41 +0000
categories: update
---

## Node Versions

Using node on multiple projects is great, until each project starts using a
different version of node `(╯°□°）╯︵ ┻━┻` Nvm is a tool that allows developers
to manage different versions of node on the same machine. Nvm works great in
Windows, Linux, and OSX; let's see if we can get it running on the Windows
Subsystem for Linux (WSL). And while we're at it, let's get it running with
fish and oh-my-fish (my preferred shell).

Nvm is a script not a executable. This means an `nvm.sh` file is placed on
your computer. To "execute" it, the path to the script is included in the
`.bashrc`, `.profile`, or `.zshrc`. 

*Note:* it **(╯°□°）╯︵ ɥsıɟ ʇɹoddus ʇou sǝop**

## Installing nvm

### Step 1 Install nvm.sh

From [the github page](https://github.com/nvm-sh/nvm) we can start by installing
the script `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash`

### Step 2, Install fenv

Next I am going to use the [fenv](https://github.com/oh-my-fish/plugin-foreign-env)
to run nvm.sh from bash with all of the appropriate environment variables set.
To install fenv run `omf install foreign-env`.

### Step 3, Setup fish function

The fish shell will allow you to setup aliases just like bash, but it also allows
developers to create functions as modules under the `.config/fish/functions/`
directory. Create a new file in this directory named `nvm.fish`. Copy and paste
the following function into the file

```bash
function nvm
    fenv source ~/.nvm/nvm.sh \; nvm $argv
end
```

## Run nvm

Now just restart your shell and start using nvm as you normally would!
