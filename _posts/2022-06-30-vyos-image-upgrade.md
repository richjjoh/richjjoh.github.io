---
title: VyOS Image Updating - Rolling
date: 2022-06-30 20:00:00 +01:00
categories: [Network, VyOS, FOSS]
tags: [VyOS]
---

# Updating VyOS - Say what now?

If you're lucky enough to have a subscription, either through payment or contribution to this amazing project you can freely download LTS images. 
For the rest of us there are two major options, building your own image or leveraging the rolling release. 

The rolling releases can be found over on their website 

https://vyos.net/get/nightly-builds/

It's important to note that these have the following disclaimer "Nightly builds are not hand-tested before upload. A basic set of automated smoke tests is executed for each build ensuring that basic functionality is working. In addition we load arbitrary configurations to ensure there are no errors during config migration and system bootup."

Rather than worrying about picking out the most recent build from the list, we can use the following link to automatically get the lastest nightly build. 

https://s3-us.vyos.io/rolling/current/vyos-rolling-latest.iso

# Checking the current version

A simple show version will return the build that's currently running. 

Command: "show version"

```bash
show version
```
Example:
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

# Backup 3 2 1

Always recommended to have a backup, well three to be precise; 2 on site and one off site. 




# Sufficent space to proceed?

SSD's are so cheap these days, I'm somewhat spoilt for space. Always worth checking there's sufficent space before proceeding; I've got 200GB free, whislt I've not monitored how little space you need I'd suggest you want atleast 5GB, or more on the safe side. This is a little large, but gives you wiggle room to download more images, revert to a previous image etc... Do what you're comfortable with. 

Command: "df -h"

```bash
df -h
```
Example:
```bash
richj@ph-gfw01:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            3.4G     0  3.4G   0% /dev
tmpfs           698M  1.6M  696M   1% /run
/dev/sda3       219G  4.5G  204G   3% /usr/lib/live/mount/persistence
/dev/loop0      425M  425M     0 100% /usr/lib/live/mount/rootfs/1.4-rolling-202204150217.squashfs
tmpfs           3.5G     0  3.5G   0% /usr/lib/live/mount/overlay
overlay         219G  4.5G  204G   3% /
tmpfs           3.5G   84K  3.5G   1% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           3.5G  132K  3.5G   1% /tmp
tmpfs           3.5G  200K  3.5G   1% /var/tmp
none            3.5G     0  3.5G   0% /etc/cni/net.d
none            3.5G  1.7M  3.5G   1% /opt/vyatta/config
tmpfs           698M     0  698M   0% /run/user/1004
```

# Insufficent Space

If you are on the tight size of disk space, you may be able to delete a previous image, run "show system image", while it'll show you the one you've booted from, you may have chosen a different boot image manually. Worth checking the versions from the previous step to ensure you don't remove a known working image.  

```bash
show system image
```
Example: 
```bash
richj@ph-gfw01:~$ show system image
The system currently has the following image(s) installed:

   1: 1.4-rolling-202204150217 (default boot)

```

If needed run the command "delete system image" - you'll be given the list again, and the ability to select which one you want to remove. Just be careful! 


# Adding the "latest" rolling image

Really simple, like the other commands - run "add system image https://downloads.vyos.io/rolling/current/amd64/vyos-rolling-latest.iso"

You'll be prompted to accept that the signature isn't valued. Rolling releases aren't signed. This increases the risk slightly that there could have been a supply chain attack, and the contents of the medium (iso) isn't quite what it makes out to be. If this is of concern, then you'll need to get a suitable subscription to obtain signed versions. Also, don't trust links from blogs like this. Obtain the links directly from the website - https://vyos.io/

You can give the image a name, save you configuration and copy over SSH keys. I keep the image name as recommended, I copy my configuration and I keep my keys. All things you can change if you like... 

Command: "add system image https://s3-us.vyos.io/rolling/current/vyos-rolling-latest.iso"

```bash
add system image https://s3-us.vyos.io/rolling/current/vyos-rolling-latest.iso
```
Example:
```bash
richj@ph-gfw01:~$ add system image https://s3-us.vyos.io/rolling/current/vyos-rolling-latest.iso
Trying to fetch ISO file from https://s3-us.vyos.io/rolling/current/vyos-rolling-latest.iso...
Downloading...
The file is 387.000 MiB.
[#########################################################################################################] 100%
Download complete.
Done.
Checking for digital signature file...
Downloading...
Failed to download https://s3-us.vyos.io/rolling/current/vyos-rolling-latest.iso.minisig.
requests.exceptions.HTTPError: 403 Client Error: Forbidden for url: https://s3-us.vyos.io/rolling/current/vyos-rolling-latest.iso.minisig
Downloading...
Failed to download https://s3-us.vyos.io/rolling/current/vyos-rolling-latest.iso.asc.
requests.exceptions.HTTPError: 403 Client Error: Forbidden for url: https://s3-us.vyos.io/rolling/current/vyos-rolling-latest.iso.asc
Do you want to continue without signature check? (yes/no) [yes] yes
Checking SHA256 checksums of files on the ISO image... OK.
Done!
What would you like to name this image? [1.4-rolling-202207010217]:
OK.  This image will be named: 1.4-rolling-202207010217
Installing "1.4-rolling-202207010217" image.
Copying new release files...
Would you like to save the current configuration
directory and config file? (Yes/No) [Yes]: yes
Copying current configuration...
Would you like to save the SSH host keys from your
current configuration? (Yes/No) [Yes]: yes
Copying SSH keys...
Running post-install script...
Setting up grub configuration...
Done.
```

Now its just a quick reboot and you'll be on on the new image - "reboot" and confirm the decision. This'll obviously mean you're unit is down for a few minutes. In my experience this is a long reboot - more so than normal. Certainly enough time to make a cup of tea. 

Command: "reboot"

```bash
reboot
```
Example:
```bash
richj@ph-gfw01:~$ reboot
Are you sure you want to reboot this system? [y/N] y
```

After you're suitable refreshed, you should be able to SSH back onto the unit and confirm the version you're on - you can either do a "show version" or "show system images"

Command: "show version"
Command: "show system images"

```bash
show version
```
```bash
show system images
```
Example:
```bash
richj@ph-gfw01:~$ show version

Version:          VyOS 1.4-rolling-202207010217
Release train:    sagitta

Built by:         autobuild@vyos.net
Built on:         Fri 01 Jul 2022 02:17 UTC
Build UUID:       48cb54f7-dd4b-46a6-bc37-1a1e0000a675
Build commit ID:  18a5f453459c92

Architecture:     x86_64
Boot via:         installed image
System type:      bare metal

Hardware vendor:  HP
Hardware model:   HP t730 Thin Client
Hardware S/N:     CZC6417JPM
Hardware UUID:    17719dd8-9d51-417a-271e-d6fce5979dc5

Copyright:        VyOS maintainers and contributors


richj@ph-gfw01:~$ show system image
The system currently has the following image(s) installed:

   1: 1.4-rolling-202207010217 (default boot)
   2: 1.4-rolling-202204150217

richj@ph-gfw01:~$

```

We can see now that my default boot has moved from "1.4-rolling-202204150217" to "1.4-rolling-202207010217". 

Time to test to make sure everythings good. If its not you have two choices. Wait another night for another release and repeat this article, or you can revert back to the image you know was functional. 

# Reverting an upgrade

I've never needed to do this, my configuration is pretty simple - I'm not pushing the boundaries of what VyOS is capable of. If you're in this situation then it's a simple command to get out of jail. 

First just need to know the name of the image you want, with a "show system image", if you have a lot hopefully you recorded this information from the previous step (check you terminal history!). 

Once you know issue the command "set system image default-boot 1.4-rolling-202204150217" obviously replacing the image version with one relevant to your system!

I can only assume after that it's another reboot, but you get another cup of tea. 

