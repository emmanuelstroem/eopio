---
date: 2017-09-30
title: "Free SSL Certificates using Certbot"
description: |
  
tags: ["linux", "ssl", "certificates", "certbot"]
author: Emmanuel ([@emmanuelstroem](https://twitter.com/emmanuelstroem))
---

**What is SSL?**
Secure Sockets Layer is a cryptographic protocol that provided communications security overa computer network.
Also makes a domain name reflect HTTPS on your browser address.

SSL is currently succeeded by TLS (Transport Layer Security) which aims majorly on providing ***privacy*** and ***data security*** between 2 communicating applications.

Setting up SSL for your domain is relatively easy and free.

Requirements:
     - Linux Server Access through SSH (Secure SHell) (sudo access)
     - Registered domain and configured DNS


STEPS:

### 1. SSH into your server:
        ssh username@server-ip

### 2. Install Certbot:

      Ubuntu:
        sudo apt update && apt upgrade
        sudo apt install certbot

      Fedora:
        sudo dnf install certbot python2-certbot-apache

      Arch Linux:
        sudo pacman -S certbot

### 3. Create folder in web root
    mkdir -p /var/www/your_site_name/.well-known/acme-challenge

### 4. Create a certificate for the domain
```certbot certonly --webroot -w /var/www/your_site_root_directory -d your_domain.com -m your_email@example.com```

Certificate: ```fullchain.pem```
and Key: ```privkey.pem```
will be generated in the path: ```/etc/letsencrypt/live/your_domain/```

### 5. Add SSL configuration to web-server:
_Nginx:_ 
    - open config with ```vim /etc/nginx/sites-available/your_site_config``` or ```vim /etc/nginx/sites-available/default```
    - Add the following lines, editing _your_domain_ variable:

        server {

            # SSL configuration
            listen 443;
            listen [::]:443;

            ssl on;
            ssl_certificate /etc/letsencrypt/live/your_domain/fullchain.pem;
            ssl_certificate_key /etc/letsencrypt/live/your_domain/privkey.pem;

            root /var/www/your_domain;

            # Add index.php to the list if you are using PHP
            index index.html index.htm index.nginx-debian.html;

            server_name your_domain;

            location / {
                try_files $uri $uri/ =404;

                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header Host $http_host;
            }

            location ~ /.well-known {
                    allow all;
                    default_type "text/plain";
            }

            client_max_body_size 50m;
        }
        
        # Redirect HTTP requests to HTTPS
        server {
            listen 80;
            listen [::]:80;

            # **www** domain is VERY important for renewal purposes
            server_name your_domain.com www.your_domain.com; 
            rewrite ^ https://$server_name$request_uri? permanent;

            root /var/www/your_domain;
            index index.html;
            location / {
                try_files $uri $uri/ =404;
            }
        }

### 6. Restart your webserver:
_Nginx:_ ```systemctl restart nginx```


### 7. Setup Auto-Renewal of Certificate
###### Perform a dry run of the renewal process.
 ```sudo certbot renew --dry-run```
 This will ONLY attempt a certificate renewal process on your domain relying on the URL: http://your_domain/.well-known/acme-challenge/some_random_key

If not dry-run fails, fix any issues especially with 404 return pages before proceeding.
 
###### Create cron job to check and autorenew your certificates before expiry.
If dry run is successful, proceed to create a cron job below. 

    sudo crontab -e

Select from the list of command-line editors: ed, nano, vim-basics or vim-tiny:

Add cron job below at the bottom of the file.

    15 3 * * * certbot renew --quiet
    
   
Checkout more about Crontabs and how to write them here: https://eopio.com/understanding-cron-tab-syntax

More reads on using certbot and letsencrypt:
https://certbot.eff.org/docs/
https://letsencrypt.org/getting-started/


Why Use LetsEncrypt?
    + The connection between the user and your server is directly secure.

Comparison to Cloudfare's Flexible SSL:
    + Flexible SSL by Cloudfare recieves all your traffic, decrypts the requests and passes them to your server unencrypted. The connection between Cloudfare and your server is ***not*** secure.

Why Crontabs?
    + 3 months certificates, free and unlimited renewals. 
Crons help to automate that renewal process for us.

Good Luck

<img src="http://eopio.com/view.gif?page=/about-me" alt="" style="display:none" hidden />
