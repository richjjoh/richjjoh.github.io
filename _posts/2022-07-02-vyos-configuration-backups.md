---
title: VyOS - Configuration Backups
date: 2022-06-30 20:00:00 +01:00
categories: [Network, VyOS, FOSS]
tags: [VyOS]
---

# VyOS Configuration Backups 
Backups aren't an exciting topic, but essential if like me you dabble and don't always get it right. There's several get out of jail features with VyOS and we'll discuss them here. 

With any backup, it's always best to follow the 3,2,1 rule. 3 copies, 2 local and atleast 1 remote. 

# Commit Confirm
There's a great feature that I'd recommend you all to consider using "commit-confirm". If you're making a somewhat risky configuration change replace "commit" with "commit-confirm". By default within 10 minutes after performing a "commit-confirm" if you don't issue a "confirm" command the system will automatically reboot, taking you back to the previous configuation. 

# Commit History
The default commit history is 100, however, this can be increased. It'll use up more disk space but the files are small. You'd have to be in a very tight disk space situation to be concerned by this. 

# Increase Commit History
We can increase the local commit history, to my knowledge there's only two real concerns in doing so. Firstly local disk space, secondly remote disk space if you're sending the archive remotely. The more history you store, depending on your rate of change more diskspace will be consumed. 

Storage these days is cheap, so I increase it to 1,000. Use any value you're comfortable with. I think this value is silly high, but as a learning tool I like knowing I could, if I wanted to easy compare my current configuration to one months ago. 

Enter configuration mode:
```bash
configure
```
Alter the commit revisions to desired value (default 100)
```bash
set system config-management commit-revisions x
```
Example:
```bash
richj@ph-gfw01# set system config-management commit-revisions 1000
[edit]
```

# Reverting to a previous commit
This is called rolling back in VyOS language, first we need to establish which previous commit we want to go back to. We can use the "compare" command for this - using the tab complete, issue a "compare" tab to view the list of available commits. You'll now be able to see just host much has changed. 


Issue compare, then use tab complete to list availble revisions. Lowest number is most recent.

Command: "Compare X" substitute x with your chosen commit revision.
```bash
compare 3
```
Example:
```bash
richj@ph-gfw01# compare 3
[edit traffic-policy shaper 1gbps class 5]
+ceiling 300mbit
[edit]
```

In the above example, compared to my current active configuration there was a traffic shaping policy applied. If I revert to this commit, I'll be reinstanting that configuyration of having a traffic shaping policy. 

A "+" denotes it will be added to your current "as is" configuration, if applied.
c
A "-" denotes it will remove from your current "as is" configuration if applied.

It may take some time to find a suitable commit. In my experience I'm only tending to go back a few commits when I've been playing with a setting or two but incremented its configuration slowly over several commits. 

Once we've located the commit we want we issue the "rollback" command. I'll rollback to my third commit, reinstating the traffic policy. 

Command: "rollback x" substitute x with your chosen commit revision, note a system reboot is required. 

```bash
rollback x
```
Example:
```bash
richj@ph-gfw01# rollback 3
Proceed with reboot? [confirm]
```

# Remote archiving
The rule of backups includes remote, off local device. How remote I'll leave up to you (consider how you'll retreive the configuration in a system outage). 

This is super easy, but needs some sort of remote storage; SFTP / SCP are perhaps the only ones I'd recommend as they provide encryption in transport protection but TFTP and FTP are supported, but unless being use locally on a trusted network I would steer away from these. 

The command you'll need is "set system config-management commit-archive location SCP://user:password@host/dir"

```bash
set system config-management commit-archive location x
```
Example:
```bash
richj@ph-gfw01# set system config-management commit-archive location scp://richj:notreallymypassword@fakehost.com/backup
s
[edit]
```
