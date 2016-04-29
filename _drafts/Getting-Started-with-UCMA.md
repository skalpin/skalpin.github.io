---
layout: post
title:  "Getting Started with UCMA"
categories: update
---

# UCMA development isn't easy

# Lync environment setup

# Requesting a certificate

# Creating Lync Entities

# Installing UCMA 5.0

# Starting a the Lync Platform

# Targeting UCMA 4.0

So you got your machine all setup for Skype for Business development. Now the client informs
you they are running Lync 2013. That should be easy right? Just install UCMA 4.0 alongside
UCMA 5.0. The problem is you can't. You can't have multiple versions of UCMA installed on the
same machine at the same time. Most of the dependencies between the different
versions are the same however, so what you can do to target the UCMA 4.0 Microsoft.Rtc.Collaboration
dll instead and rebuild your project. Most likely you won't run into any issues.