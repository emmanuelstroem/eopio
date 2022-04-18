---
date: 2018-10-22
title: "Linux Process Manager: Upstart"
description: |
  
tags: ["linux", "process", "manager", "upstart"]
author: Emmanuel ([@emmanuelstroem](https://twitter.com/emmanuelstroem))
---

### Definiation:
Upstart is an event-based replacement for the /sbin/init daemon which handles starting of tasks and services during boot, stopping them during shutdown and supervising them while the system is running.

Upstart Processes reside in /etc/init/

### Go to process directory
```cd /etc/init/```

### Create Conf file
Create your process config file in there
e.g process_name.conf using your preferred editor.
```vim process_name.conf```

example of Upstart config file:
```
description "Job that runs the foo daemon"

# start in normal runlevels when disks are mounted and networking is available
start on runlevel [2345]

# stop on shutdown/halt, single-user mode and reboot
stop on runlevel [016]

env statedir=/var/cache/foo

# create a directory needed by the daemon
pre-start exec mkdir -p "$statedir"

exec /usr/bin/foo-daemon --arg1 "hello world" --statedir "$statedir"
```

### Check Syntax
Check your process conf file for syntax errors using:
```init-checkconf -d /etc/init/process_name.conf```

### Reload Upstart
Relaod upstart to recognise your process
```initctl reload-configuration```

### Control your process
Control the process using:
```sudo start | stop process_name```


### Upstart vs Systemd Commands
|Operation   | Upstart Command  | Systemd equivalent  | Notes   |
|---|---|---|---|
|Start service |start $job|systemctl start $unit or service $unit start|   |
|Stop service |stop $job|systemctl stop $unit or service $unit stop|   |
|Restart service |restart $job|systemctl restart $unit or service $unit restart|   |
|See status of services|initctl list|systemctl list or service --status-all|   |
|Check configuration is valid|init-checkconf /etc/init/$job.conf|systemd-analyze verify <unit_file>|   |
|Show job environment|initctl list-env|systemctl show-environment|   |
|Set job environment variable|initctl set-env foo=bar|systemctl set-environment foo=bar|   |
|Remove job environment variable|initctl unset-env foo|systemctl unset-environment foo|   |
|View job log|cat /var/log/upstart/$job.log|sudo journalctl -u $unit|   |
|tail -f job log|tail -f /var/log/upstart/$job.log|sudo journalctl -u $unit -f|   |
|Show relationship between services|initctl2dot|systemctl list-dependencies --all|   |

More detailed explanation to the slightest differences between Upstart and Systemd can be found here:
[https://wiki.ubuntu.com/SystemdForUpstartUsers](http://https://wiki.ubuntu.com/SystemdForUpstartUsers)
