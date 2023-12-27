---
layout: post
title: systemd-logind, Unit suspend.target is masked, refusing operation.
description: >
  systemd-logind, auth.log
sitemap: false
hide_last_modified: true
categories: [linux, server, log, systemd, logind]
tags:       [linux, server, log, systemd, logind]
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Recommended Specifications

- ubuntu 22.04 live server amd64

## Reference site

- [Ubuntu man page]{:.heading.flip-title}  &rarr; Ubuntu manual

## fix

- check auth.log

```sh
$ tail /var/log/auth.log

Dec 27 00:46:08 dev-server systemd-logind[774]: Suspending...
Dec 27 00:46:08 dev-server systemd-logind[774]: Unit suspend.target is masked, refusing operation.
Dec 27 00:46:08 dev-server systemd-logind[774]: Failed to execute suspend operation: Permission denied
```

- check disk used

```sh
$ df -h

Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              1.6G  2.0M  1.6G   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv  232G   35G  187G  16% /
tmpfs                              7.8G     0  7.8G   0% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
/dev/nvme0n1p2                     2.0G  252M  1.6G  14% /boot
/dev/nvme0n1p1                     1.1G  6.1M  1.1G   1% /boot/efi
tmpfs                              1.6G  4.0K  1.6G   1% /run/user/1000
```

- modify logind.conf &rarr; `HandleLidSwitch=ignore`

```sh
# file : /etc/systemd/logind.conf
$ vi /etc/systemd/logind.conf

#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it under the
#  terms of the GNU Lesser General Public License as published by the Free
#  Software Foundation; either version 2.1 of the License, or (at your option)
#  any later version.
#
# Entries in this file show the compile time defaults. Local configuration
# should be created by either modifying this file, or by creating "drop-ins" in
# the logind.conf.d/ subdirectory. The latter is generally recommended.
# Defaults can be restored by simply deleting this file and all drop-ins.
#
# Use 'systemd-analyze cat-config systemd/logind.conf' to display the full config.
#
# See logind.conf(5) for details.

[Login]
...skipping...
#HandleLidSwitch=suspend
HandleLidSwitch=ignore
...skipping...

```

- check systemd-logind.service

```sh
$ service systemd-logind status

● systemd-logind.service - User Login Management
     Loaded: loaded (/lib/systemd/system/systemd-logind.service; static)
     Active: active (running) since Thu 2023-12-14 09:04:27 UTC; 1 week 5 days ago
       Docs: man:sd-login(3)
             man:systemd-logind.service(8)
             man:logind.conf(5)
             man:org.freedesktop.login1(5)
   Main PID: 774 (systemd-logind)
     Status: "Processing requests..."
      Tasks: 1 (limit: 18809)
     Memory: 4.6M
        CPU: 1w 2d 21h 30min 29.269s
     CGroup: /system.slice/systemd-logind.service
             └─774 /lib/systemd/systemd-logind

Dec 27 00:48:32 dev-server systemd-logind[774]: Failed to execute suspend operation: Permission denied
Dec 27 00:48:32 dev-server systemd-logind[774]: Failed to execute suspend operation: Permission denied
Dec 27 00:48:32 dev-server systemd-logind[774]: Failed to execute suspend operation: Permission denied
Dec 27 00:48:32 dev-server systemd-logind[774]: Failed to execute suspend operation: Permission denied
```

- restart systemd-logind.service

```sh
$ service systemd-logind restart

==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to restart 'systemd-logind.service'.
Authenticating as: adminUserAngel (devgroup)
Password:
==== AUTHENTICATION COMPLETE ===
```

{:.related-posts.faded}

## truncate log

1. **`sudo truncate ./auth.log --size 0`**
2. `truncate /opt/package/logs/*.log --size 0`
3. `sudo sh -c 'cat /dev/null > /var/log/syslog'`

{:.related-posts.faded}

[Ubuntu man page]:https://manpages.ubuntu.com/manpages/trusty/man5/logind.conf.5.html