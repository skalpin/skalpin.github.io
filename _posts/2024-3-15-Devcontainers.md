---
layout: post
title:  "Devcontainers"
date:   2024-03-15 00:00:00 +0000
categories: update
---

Development containers have completely changed my local development workflow. They make it easier
for me to use the correct version of the tools I need for each specific project, and they help
keep the tools consistent across my team. So what is a devcontainer?

The concept of a devcontainer is simple. I want to make changes to an application which means I need
to be able to build and run it locally, however I don't know what tools I need or how to install them.
In some cases I cannot install the correct version of a tool. This can happen when I am working on
separate projects that need different versions of a tool that cannot be installed at the same time on my
operating system.

For example, I am writing this blog using Jekyll. I don't necessarily want to install ruby, gem, and
jekyll on my system to update the blog. It's much easier to spin up a container with the tools installed
and make the changes in there.

For this walkthrough I am going to be using Rancher, Visual Studio Code, and the Devcontainer extension.

## Setup Rancher

This is the most difficult step getting started with devcontainers. You need to have something to run
the containers, and docker is my tool of choice. Since Docker Desktop is no longer free for Enterprise
developers, I have selected Rancher Desktop to get docker setup on my system. You can download and
install it from [https://rancherdesktop.io/](https://rancherdesktop.io/). You do not need to setup
Kubernetes for this to work properly.

## Setup Visual Studio Code

I'm assuming you already have Visual Studio Code, but just in case yo don't download and install it from
[https://code.visualstudio.com/download](https://code.visualstudio.com/download)

In the extensions window, search for "Dev Containers" or `ms-vscode-remote.remote-containers`.

## Setup your project

Open your project folder in Visual Studio Code. From the command window find `Dev Containers:Reopen in Container`. This will give you a menu to add the configuration to the user data or to the workspace.
I suggest you choose workspace so that it saves the configuration with your project. That way any
developer who needs to make changes has access to this container.

Then all you need to do is select the base container image you want to work with. For my blog
I am using Jekyll, so I selected an image with Jekyll installed.

## Edit your project from a container

What happes now is the VS Code extension pulls the image, and mounts the folder you had open
into a docker volume and attaches it to the conatiner. It also sets up a non-root user
(probably vscode) in the container so that edits to files are mapped to the appropriate
user on your host system and not root. Additionally it sets up git credentials so you can
make changes and push them to the remote server.

You'll notice it created a `.devcontainer` folder with a `devcontainer.json` file. This file
tells VSCode that your project can use a devcontainer, and which image to use for it.

## More information

For more information checkout [https://containers.dev/](https://containers.dev/)
