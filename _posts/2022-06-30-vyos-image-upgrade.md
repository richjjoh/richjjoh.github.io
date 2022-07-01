---
title: VyOS Image Updating - Rolling
date: 2022-06-30 20:00:00 +01:00
categories: [Network, VyOS, FOSS]
tags: [VyOS]
---

# Updating VyOS

If you're lucky enough to have a subscription, either through payment or contribution to this amazing project you can freely download LTS images. 
For the rest of us there are two major options, building your own image or leveraging the rolling release. 

The rolling releases can be found over on their website 

https://vyos.net/get/nightly-builds/

It's important to note that these have the following disclaimer "Nightly builds are not hand-tested before upload. A basic set of automated smoke tests is executed for each build ensuring that basic functionality is working. In addition we load arbitrary configurations to ensure there are no errors during config migration and system bootup."

Rather than worrying about picking out the most recent build from the list, we can use to automatically get the lastest nightly build. 

https://s3-us.vyos.io/rolling/current/vyos-rolling-latest.iso


# Updating

This isn't a best practice guide, its merely what I do. 

Check disk space - I want atleast 10GB free, just to safe. Images are about 400MB, but I want some wiggle room. 
Check current version - Just so I know, as we're using "vyos-rolling-latest.iso" its nice to have a record of where I was. 
Crack on and grow some balls. 


```bash
richj@ph-gfw01:~$ show version

Version:          VyOS 1.4-rolling-202204150217
Release train:    sagitta

Built by:         autobuild@vyos.net
Built on:         Fri 15 Apr 2022 02:17 UTC
Build UUID:       211ab7da-8344-4bd3-a98e-71fdf9bf7667
Build commit ID:  d028db1b242fd2

Architecture:     x86_64
Boot via:         installed image
System type:      bare metal

Hardware vendor:  HP
Hardware model:   HP t730 Thin Client
Hardware S/N:     CZC6417JPM
Hardware UUID:    17719dd8-9d51-417a-271e-d6fce5979dc5

Copyright:        VyOS maintainers and contributors
```