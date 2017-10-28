---
layout: post
title: Building a New Lab
author: Tommy Becker
date: '2017-10-28 13:19'
excerpt: 'Let's build a new lab...'
feature: assets/img/systems.jpeg
comments: true
tags:
    - lab
    - powershell
---
# The Lab setup laptop
## The Laptop

The other night, my laptop decided to update and something went seriously sideways and I wound up rebuilding the whole thing. But, I saved everything, luckily. I moved everything I felt was important over to a network drive and just wiped out the whole thing. I've been working on a few scripts that would help me rebuild my system from nothing. There's a few things that you can't do, like go and log into all of your websites that require two-factor authentication. But, there are a bunch of tasks that can make you life easier.

The first tool that I install and use is [chocolatey](https://chocolatey.org/)\. Chocolatey is a package management framework like YUM or APT in Linux. With a few simple commands you can install, update and maintain all of your commonly used apps.

Here's a script I wrote to kick-start my initial build of a workstation...

{% gist mockmyberet/01bb3327c679b6eb74ab9c2791c5cc05 %}

## The lab

So, now we have a laptop that needs a lab to work with AD, DNS and DHCP or anything else you want to work with.
