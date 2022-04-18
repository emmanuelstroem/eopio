---
date: 2019-02-01
title: "Running Nginx using Docker Compose"
description: |
  
tags: ["linux", "nginx", "docker", "docker compose"]
author: Emmanuel ([@emmanuelstroem](https://twitter.com/emmanuelstroem))
---

Pre-Requisites:
Docker:
Install Using Convenience Script:
```
curl -fsSL https://get.docker.com -o get-docker.sh && sudo sh get-docker.sh
```

2. Docker-Compose:     
Install Using:
```
sudo apt-get install docker-compose
```

Steps
Create `docker-compose.yaml` file with content:
```yaml
version: "3"
services:
  nginx:
    image: nginx:latest
    restart: always
    ports:
      - "80:80"
    networks:
      - nginxnetwork
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./nginx/sites-available:/etc/nginx/conf.d
networks:
  nginxnetwork:
```

2. Create directory `nginx` to hold configurations to be mounted into the container.
3. Create `nginx/nginx.conf` file with this content:
Add all your custom configs if any.
```conf
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*;
}
```

4. Create all your nginx site configs in the dirctory `nginx/sites-available`
5. Create a `docker-compose-nginx.service` file in `/etc/systemd/system/`	with the content:
```ini
[Unit]
Description=Docker Compose NGINX Service
Requires=docker.service
After=docker.service network.target

[Service]
Type=oneshot
RemainAfterExit=yes
WorkingDirectory=/home/mvdev
ExecStart=/usr/local/bin/docker-compose up -d
ExecStop=/usr/local/bin/docker-compose down
TimeoutStartSec=0

[Install]
WantedBy=multi-user.target
```

6. Enable the service:
```
sudo systemctl enable docker-compose-nginx.service
```

6. Reload systemctl to read the service
```
sudo systemctl daemon-reload
```

7. Start the service:
```
sudo service docker-compose-nginx start
```

8. Verify its running using:
```
sudo service docker-compose-nginx status
```

Now you can create as many configs as you want without rebuilding or committing to nginx docker image.
Also, on reboot, our service will handle everything for us.
Good Luck.
Questions/Contributions/Criticisms are more than welcome.
