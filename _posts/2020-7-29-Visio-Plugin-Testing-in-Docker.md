---
layout: post
title:  "Visio Plugin Testing in Docker"
date:   2020-07-28 10:50:00 +0000
categories: update
---

## Problem

I am currently working with a plugin to Visio. We have been writing and running automated tests using the [WinAppDriver](https://github.com/microsoft/WinAppDriver). This driver takes COM endpoints and exposes them as a web service so that your automated tests can be written just like any other website using Selinum. The issue is, when UI tests run locally, they take over the whole work station so you can't multi task or work on anything else. Additionally I would like to find a way to automate these tests as part of a pipeline so they can be run more frequently.

## Solution 1 - Setup a virtual machine

This works but it feels heavy. I still cannot run tests in parallel. However I can run tests without taking over my main machine which is good because it means I can still respond to emails while the tests run. It would be better if I could get Visio running in a docker container and spin up several instances so I can run tests in parallel.

## Install Visio in a Docker Container

The first hurdle is going to be installing Office, and specifically Visio into a docker container. I got a lot of help from this blog post [Install Microsoft Office In Windows Container](https://medium.com/rkttu/install-microsoft-office-in-windows-container-ce05877138fd). Though I only care about Visio, and we only support the 64 bit version of Visio. I modified the office.xml configuration to look like

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="Monthly">
    <Product ID="VisioProRetail">
      <Language ID="en-us" />
    </Product>
  </Add>
   <Updates Enabled="TRUE" Channel="Monthly" />
   <Display Level="None" AcceptEULA="TRUE" /> 
   <Property Name="AUTOACTIVATE" Value="1" /> 
</Configuration>
```

And my docker file looks like

```docker
#download odt setup files
FROM mcr.microsoft.com/windows:1903 AS build
WORKDIR C:\\odtsetup
ADD https://download.microsoft.com/download/2/7/A/27AF1BE6-DD20-4CB4-B154-EBAB8A7D4A7E/officedeploymenttool_11617-33601.exe odtsetup.exe
RUN odtsetup.exe /quiet /norestart /extract:C:\\odtsetup

#download office files for the selected configuration
FROM mcr.microsoft.com/windows:1903 AS download
WORKDIR C:\\odtsetup
COPY --from=build C:\\odtsetup\\setup.exe .
COPY config.xml C:\\config.xml
RUN setup.exe /download C:\\config.xml

#copy setup files and run setup
FROM mcr.microsoft.com/windows:1903
WORKDIR C:\\odtsetup
COPY --from=build C:\\odtsetup\\setup.exe .
COPY --from=download C:\\odtsetup\\Office .
COPY config.xml C:\\config.xml
RUN setup.exe /configure C:\\config.xml
WORKDIR /

#remove odt setup
RUN rmdir /s /q C:\\odtsetup

#create a new office object that was installed
RUN powershell -Command new-object -comobject visio.application
RUN mkdir C:\\Windows\\SysWOW64\\config\\systemprofile\\Desktop

#activate visio
RUN cscript "C:\Program Files\Microsoft Office\Office16\OSPP.VBS" /inpkey:<your license key>

VOLUME C:\\data
```

The next step will be to install the plugin we are working on, the win app driver, and start a selenium hub node.

## To be continued...