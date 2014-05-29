---
layout: post
title: Removing AppName_Deploy Virtual Directory from a Site Published with Web Deploy
description: "By default, webdeploy.exe will create a virtual directory named YourAppName_deploy in IIS and deploy your site there.   This is usually not desirable.   Here's how to change that."
category: Deployment
tags: 
  - IIS
  - WebDeploy
published: true
---

By default, when using webdeploy.exe,  your application will get deployed to a virtual directory similar to ApplicationName_deploy.  This means instead of your page urls based of the site root, they will be based off of /ApplicationName_deploy, eg. http://*site*/ApplicationName_deploy/.
If you don’t like this, you can change by adding the following to your .csproj file:

    <DeployIisAppPath>Default Web Site/</DeployIisAppPath>

This is a per configuration setting, so you must add it to each configuration.  For example:
  
    <PropertyGroup Condition="'$(Configuration)|$(Platform)' == 'UAT|AnyCPU'">
        <OutputPath>bin\</OutputPath>
        <DeployIisAppPath>Default Web Site/</DeployIisAppPath>
    </PropertyGroup>

This setting used to be available in the project properties within VS, but in VS2013 at least, it is not.
