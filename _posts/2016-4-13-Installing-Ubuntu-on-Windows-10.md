---
layout: post
title:  "Installing Ubuntu on Windows 10"
date: 2016-4-13
categories: update
---

# Get Excited

Because I'm a Windows .NET developer by day and a wannabe Ubuntu developer by night this new
feature in Windows 10 is especially exciting for me so what am I supposed to do besides
write a new blog article about it.

# Windows 10 Build 14316

To start with you need to get the Windows 10 Insider Preview build 14316.
The problem is, this cannot just be downloaded and installed. Using your MSDN license
(or whatever) you should be able to find  Windows 10 Insider Preview build 14295.

Then you need to set yourself up for insider preview builds from Microsoft. To do this
go to Start Menu -> Settings -> Update & security -> Advanced options. From there
you will see instructions on how to setup your account to receive the builds. You also
need to make sure you are setup to receive builds "fast".

One more thing to note: I had to wait 24 hours to receive an update to 14316 (bummer).

# Enable the Linux Feature

Good! You've now got Windows Insider Preview 14316 installed. No you need to right click on
the start menu and go to "Programs and Features". Then navigate to "Turn Windows features on or off".
Once you're looking at the windows features list select the "Windows Subsystem for Linux (Beta)".
This feature requires a computer restart.

# Installing bash

So installing the Linux Subsystem will give you a new program called bash. Run it.
You will be asked if you would like to install bash. Type 'y' and hit enter (duh). This will go
download and install what you need to get going with bash. When it's complete you will be presented
with a `root@localhost:~#` prompt. As you probably noticed you're root... so be careful.

# Updating bash

The next thing you'll need to do is update your package list. `apt-get update` will do it, maybe.
I ran into some issues with my network. None of the sources could be found. In fact a simple
`ping www.google.com` didn't execute successfuly. It turns out there is a minor bug with the bash
and IPv6 so you need to edit your sources list and move the IPv6 ones to the bottom or remove them
completely. My generated `/etc/resolv.conf` looked like this:

```
nameserver 2001:558:feed::2
nameserver 2001:558:feed::1
domain hsd1.mn.comcast.net.
nameserver 192.168.0.1
```

Open up your favorite editor (vim, emacs, pico) and edit `/etc/resolv.conf` to put the IPv6 addresses
at the bottom of the list.

```
domain hsd1.mn.comcast.net.
nameserver 8.8.8.8
nameserver 192.168.0.1
nameserver 2001:558:feed::2
nameserver 2001:558:feed::1
```

**Now** you should be able to run `apt-get update && apt-get upgrade` to upgrade your systems package lists.
