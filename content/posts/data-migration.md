+++
title = "Data Migration"
date = "2022-01-22T22:27:42+01:00"
author = "Kaj"
authorTwitter = "@bykaj" #do not include @
cover = ""
tags = ["data", "unraid", "truenas", "zfs"]
keywords = ["", ""]
description = "Data migration steps from TrueNAS Core to Unraid."
showFullContent = false
readingTime = true
+++
### Context
The data migration uses the existing TrueNAS ZFS pool `vault` as source. This pool is mounted as an Unassigned Device. Then using `rsync` to copy data to the designated shares on Unraid. Possible scenario is to use the *root share* mechanism (e.g. a share with root access on ``/mnt/``).

To utilize the full speed of the SATA bus, the parity function of the Unraid array must be disabled (eg. no assigned parity disk in the configuration). After data transfer the parity disk must be (re)build. Also, no cache pools should be active on the designated shares.

### Filesystem structure
Unraid filesystem structure:
- Physical data is in `/mnt/disk#` (\# = nr. of disk in array)
- Shares are in `/mnt/user/`
- Unassigned Devices are in `/mnt/disks/`
- Remote shares are in `/mnt/remotes/`


### Plug-ins
Needed Unraid plug-ins:
- Community Addon
- Unassigned Devices
- Unassigned Devices Plus (Addon)

For the duration of the migration only (can be disabled/removed afterwards):
- ZFS
- ZFS Companion



### ZFS
From the forum (April 2017): https://forums.unraid.net/topic/56740-freenas-to-unraid/?do=findComment&comment=556877

> If you only have one server but a backup of all important data you can use the community applications plugin on your unraid to install the zfs plugin. 
> 
> At boot time if there is a zfs pool detected in the server it gets mounted at /yourZFSpoolname. Then you can use a local rsync or cp command to copy your pool to the unraid drives. For example I use: "rsync -amvhW --no-compress --progress --stats --exclude .DS_Store /pool/ /mnt/user/data"
> 
> I tested this with a degraded test pool and it worked for me but it could be dangerous and it was not that much faster then a rsync over gigabit ethernet.

### rsync

rsync command:
```bash
rsync -avhmWP --no-compress --exclude ".DS_Store" --chown=nobody:users /vault/<folder>/* /mnt/user/<share>
```


Options:
- `r` = recursive
- `l` = copy symlinks as symlinks
- `t` = preserve times
- `v` = verbose
- `h` = human-readable
- `m` = prune empty directory chains from file-list
- `W` = copy files whole (w/o delta-xfer algorithm)
- `progress` = show progress during transfer
- `no-compress` = NO compress file data during the transfer


New Permissions tool in Unraid: 
chmod options: u-x,go-rwx,go+u,ugo+X

### Folders
- [x] archive
- [x] backups
- [x] downloads
- [x] ~~homes~~
- [x] iocage
- [x] ~~jails~~
- [x] media
- [x] private
- [x] ~~public~~
- [x] software

  



### Last steps
**IMPORTANT:** Check of all the data is copied correctly! After this step there's no backup anymore (old disks getting repurposed) and also no parity disk!

All done? Go to [[3. Finishing Steps|Finishing Steps]].


