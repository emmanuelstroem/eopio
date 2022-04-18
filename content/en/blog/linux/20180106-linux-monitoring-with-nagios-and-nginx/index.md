---
date: 2018-01-06
title: "Monitoring with Nagios and Nginx"
description: |
  
tags: ["linux", "monitoring", "nagios", "nginx"]
author: Emmanuel ([@emmanuelstroem](https://twitter.com/emmanuelstroem))
---

**[Nagios](https://www.nagios.org/)** provides enterprise-class Open Source IT monitoring, network monitoring, server and applications monitoring.

We'll cover Linux (Ubuntu) Server Monitoring.

Update your system.
```apt update``` and optionally upgrade using ```apt upgrade```

Install the required dependencies using:
``` 
apt-get install -y build-essential dos2unix gcc git libmcrypt4 libpcre3-dev ntp unzip make python2.7-dev python-pip re2c supervisor unattended-upgrades whois vim libnotify-bin pv cifs-utils 
```

Run this command to Add these missing software sources to your distro:
``` 
apt-get install -y software-properties-common curl
apt-add-repository ppa:ondrej/php -y
apt-add-repository ppa:nginx/development -y 
```

install PHP Specific Dependencies using :
``` 
apt-get install php7.1-cli php7.1-dev \
php7.1-pgsql php7.1-sqlite3 php7.1-gd \
php7.1-curl php7.1-memcached \
php7.1-imap php7.1-mysql php7.1-mbstring \
php7.1-xml php7.1-zip php7.1-bcmath php7.1-soap \
php7.1-intl php7.1-readline php-xdebug 
```

Set these PHP Module variables:
```
sudo sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/7.1/cli/php.ini
sudo sed -i "s/display_errors = .*/display_errors = On/" /etc/php/7.1/cli/php.ini
sudo sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/7.1/cli/php.ini
sudo sed -i "s/;date.timezone.*/date.timezone = UTC/" /etc/php/7.1/cli/php.ini
```

Install PHP-FPM and NGINX 
apt-get install nginx php7.1-fpm

# Setup Some PHP-FPM Options
```
echo "xdebug.remote_enable = 1" >> /etc/php/7.1/mods-available/xdebug.ini
echo "xdebug.remote_connect_back = 1" >> /etc/php/7.1/mods-available/xdebug.ini
echo "xdebug.remote_port = 9000" >> /etc/php/7.1/mods-available/xdebug.ini
echo "xdebug.max_nesting_level = 512" >> /etc/php/7.1/mods-available/xdebug.ini
echo "opcache.revalidate_freq = 0" >> /etc/php/7.1/mods-available/opcache.ini
```

```
sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/7.2/fpm/php.ini
sed -i "s/display_errors = .*/display_errors = On/" /etc/php/7.1/fpm/php.ini
sed -i "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/" /etc/php/7.1/fpm/php.ini
sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/7.1/fpm/php.ini
sed -i "s/upload_max_filesize = .*/upload_max_filesize = 100M/" /etc/php/7.1/fpm/php.ini
sed -i "s/post_max_size = .*/post_max_size = 100M/" /etc/php/7.1/fpm/php.ini
sed -i "s/;date.timezone.*/date.timezone = UTC/" /etc/php/7.1/fpm/php.ini
```

### Disable XDebug On The CLI
``` sudo phpdismod -s cli xdebug ```

### Copy fastcgi_params to Nginx because they broke it on the PPA
```
cat > /etc/nginx/fastcgi_params << EOF
fastcgi_param	QUERY_STRING		\$query_string;
fastcgi_param	REQUEST_METHOD		\$request_method;
fastcgi_param	CONTENT_TYPE		\$content_type;
fastcgi_param	CONTENT_LENGTH		\$content_length;
fastcgi_param	SCRIPT_FILENAME		\$request_filename;
fastcgi_param	SCRIPT_NAME		\$fastcgi_script_name;
fastcgi_param	REQUEST_URI		\$request_uri;
fastcgi_param	DOCUMENT_URI		\$document_uri;
fastcgi_param	DOCUMENT_ROOT		\$document_root;
fastcgi_param	SERVER_PROTOCOL		\$server_protocol;
fastcgi_param	GATEWAY_INTERFACE	CGI/1.1;
fastcgi_param	SERVER_SOFTWARE		nginx/\$nginx_version;
fastcgi_param	REMOTE_ADDR		\$remote_addr;
fastcgi_param	REMOTE_PORT		\$remote_port;
fastcgi_param	SERVER_ADDR		\$server_addr;
fastcgi_param	SERVER_PORT		\$server_port;
fastcgi_param	SERVER_NAME		\$server_name;
fastcgi_param	HTTPS			\$https if_not_empty;
fastcgi_param	REDIRECT_STATUS		200;
EOF
```

### Set The Nginx & PHP-FPM User
```
sed -i "s/user www-data;/user www-data;/" /etc/nginx/nginx.conf
sed -i "s/# server_names_hash_bucket_size.*/server_names_hash_bucket_size 64;/" /etc/nginx/nginx.conf

sed -i "s/user = www-data/user = www-data/" /etc/php/7.1/fpm/pool.d/www.conf
sed -i "s/group = www-data/group = www-data/" /etc/php/7.1/fpm/pool.d/www.conf

sed -i "s/listen\.owner.*/listen.owner = www-data/" /etc/php/7.1/fpm/pool.d/www.conf
sed -i "s/listen\.group.*/listen.group = www-data/" /etc/php/7.1/fpm/pool.d/www.conf
sed -i "s/;listen\.mode.*/listen.mode = 0666/" /etc/php/7.1/fpm/pool.d/www.conf
```

Create Nagios User
```
useradd nagios
usermod -a -G nagios www-data
```

Download Nagios Using:
```
wget https://assets.nagios.com/downloads/nagioscore/releases/nagios-4.3.4.tar.gz
```

Extract the download using:
```
tar -zxvf nagios-4.3.4.tar.gz
```

Go into the extracted folder
```cd nagios-4.3.4```

configure the installer using:
```./configure --with-nagios-group=nagios --with-command-group=www-data --with-mail=/usr/sbin/sendmail```

If all runs fine. Execute: ```make all``

If that runs successuflly without any issues/errors and the configuration console output appears OK with you, then proceed to run the commands below:

``` 
make install 
make install-commandmode
make install-init
make install-config
```

Enable the Nagios service using:
``` systemctl enable nagios.service ```

## Nagios Plugins
Download using:
``` wget https://nagios-plugins.org/download/nagios-plugins-2.2.1.tar.gz ```

Extract Using:
``` tar -zxvf nagios-plugins-2.2.1.tar.gz && cd nagios-plugins-2.2.1 ```

Configure Using:
``` ./configure --with-nagios-user=nagios --with-nagios-group=www-data --with-openssl ```

Compile Using:
``` make ```

Install Using:
``` make install ```

## NRPE
Download to your local machine from:
http://downloads.sourceforge.net/project/nagios/nrpe-2.x/nrpe-3.1.1/nrpe-3.2.1.tar.gz

Push to remote machine using:
```scp -P port_number Downloads/nrpe-3.2.1.tar.gz manu@eopio.com:/var/www/ ```
where _port_number_ is the ssh port number of your server. Default is 22

In your remote server, Extract the newly uploaded file using:
``` tar -zxvf nrpe-3.2.1.tar.gz && cd nrpe-3.2.1 ```

Configure it Using:
``` ./configure --enable-command-args --with-nagios-user=nagios --with-nagios-group=www-data --with-ssl=/usr/bin/openssl --with-ssl-lib=/usr/lib/x86_64-linux-gnu --with-need-dh=no```

**dh** sometimes causes config issues/errors so i set the glag ```--with-need-dh=no```

Compile and Install Using:
```
make install
make install-init
make install-config
```

Enalbe NRPE Service:
``` systemctl enable nrpe.service ```


## Configure Nagios
Open Config in your favourite terminal editor. I use vim:
``` vim /usr/local/nagios/etc/nagios.cfg ```

Uncomment ```#cfg_dir=/usr/local/nagios/etc/servers``` by Removing the **#** symbol

Create the direcory where we'll put all our server config files.
``` mkdir -p /usr/local/nagios/etc/servers ```

### Configure Nagios Admin User
Install apache2-utils using:
``` apt get install apache2-utils```

Create nagios admin user account using:
``` sudo htpasswd -b -c /usr/local/nagios/etc/.htpasswd nagiosadmin nagiospassword```
```-b``` enables the use of password from commandline rather than prompting for it.
```-c``` creates a new file. Overwrites any existing in the same path.

Add the following setting to your Nginx Web server Config in /etc/nginx/sites-available
```
  auth_basic "Private";
  auth_basic_user_file /usr/local/nagios/etc/.htpasswd;
```



### Configure Nagios Contact
Open the contact config file in your editor:
``` vim /usr/local/nagios/etc/objects/contacts.cfg ```

Change the default contact address to your email address
``` 
email   nagios@localhost   ; <<***** CHANGE THIS TO YOUR EMAIL ADDRESS ******
```

### Configure NRPE Command
Open the commands config file in your editor:
``` vim /usr/local/nagios/etc/objects/commands.cfg ```

Append the following to the end of the file.
```
define command {
    command_name check_nrpe
    command_line $USER1$/check_nrpe -H $HOSTADDRESS$ -c $ARG1$
}
```
This allows you to see _check_nrpe_ command in your Nagios service definition.

### NGINX Config
Ensure that your Nginx site config file in ```/etc/nginx/sites-available/``` looks like this:

```
    server {
    

        listen 443;
        listen [::]:443;

        # force https-redirect

        ssl on;
        ssl_certificate /etc/letsencrypt/live/eopio.com/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/eopio.com/privkey.pem;

        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

        add_header Strict-Transport-Security "max-age=31536000";


        server_name localhost;

        root /var/www/ghost/system/nginx-root;

        index index.php index.html;

        location / {
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header Host $http_host;
            proxy_pass http://127.0.0.1:2368;

        }

        location ~ /.well-known {
            allow all;
    #	root /var/www/ghost/system/nginx-root;
        default_type "text/plain";	
        }

        location ~ /js {
        allow all;
        root /var/www/ghost/system/nginx-root;
        default_type "text/javascript";
        }

        location /apple-app-site-association {
        default_type application/json;
       }

        location /nagios {
            alias /usr/local/nagios/share;
            allow all;
            
            auth_basic "Private";
            auth_basic_user_file /usr/local/nagios/etc/.htpasswd;
    
            location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_param SCRIPT_FILENAME $request_filename;
                fastcgi_param AUTH_USER $remote_user;
                fastcgi_param REMOTE_USER $remote_user;
                fastcgi_pass unix:/run/php/php7.1-fpm.sock;
            }
            location ~ \.cgi$ {
                root /usr/local/nagios/sbin;
                rewrite ^/nagios/cgi-bin/(.*)\.cgi /$1.cgi break;
                include /etc/nginx/fastcgi_params;
                fastcgi_param SCRIPT_FILENAME $request_filename;
                fastcgi_param AUTH_USER $remote_user;
                fastcgi_param REMOTE_USER $remote_user;
                fastcgi_pass unix:/var/run/fcgiwrap.socket;
            }
        }

        location ~ ^/nagiosgraph/cgi-bin/(.*\.cgi)$ {
            alias /usr/local/nagiosgraph/cgi/$1;
            include /etc/nginx/fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $request_filename;
            fastcgi_param AUTH_USER $remote_user;
            fastcgi_param REMOTE_USER $remote_user;
            fastcgi_pass unix:/var/run/fcgiwrap.socket;
        }


        location /nagiosgraph {
            alias /usr/local/nagiosgraph/share;
        }

        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/run/php/php7.1-fpm.sock; 
        }


        client_max_body_size 50m;
    }


    server {
        listen 80;
        listen [::]:80;
    #
        server_name eopio.com;
    #	rewrite ^ https://$server_name$request_uri? permanent;
    #
        root /var/www/ghost/system/nginx-root;
        index index.html;
    #
        location / {
            rewrite ^ https://$server_name$request_uri? permanent;
    #		try_files $uri $uri/ =404;
        }
    }
```

#### Start Nagios service
```systemctl start nagios```
#### and restart nginx
```systemctl restart nginx```

If the console has no output, then all is good. 
Otherwise, Keep Calm and read the error message.

If no errors, add nagios to startup commands:
``` sudo ln -s /etc/init.d/nagios /etc/rcS.d/S99nagios ```


Goto ```your_domain/nagios``` e.g [eopio nagios](https://eopio.com/nagios)
Enter your nagios admin username and password.

Get Monitoring!

Thank you and Feel free to reach out!
