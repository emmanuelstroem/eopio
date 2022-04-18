---
date: YYYY-mm-dd
title: "Draft Post Template"
description: |
  
tags: [""]
draft: true
author: Emmanuel ([@emmanuelstroem](https://twitter.com/emmanuelstroem))
---


### Services Directory
> /etc/systemd/system/

### Create service with ext .service
e.g blog.service

### Copy | Move service file to service directory
cp blog.service /etc/systemd/system/

### Enable the service
systemctl enable blog.service

### Reload Service Daemons
systemctl daemon reload

#### Control Service
systemctl start|stop|restart blog.service
or
service blog start | stop | restart

### Disable Service
systemctl disable blog.service
