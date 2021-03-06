# Intro
This is a shell script for setting up Laravel Production environment on Ubuntu 16.04 system

## Software list

- Ubuntu 16.04
- Git
- PHP7.2
- Nginx
- MySQL
- Sqlite3
- Composer
- Node6 (With Yarn, PM2, Bower, Grunt, and Gulp)
- Redis
- Mencached
- Beanstalkd

## Installation

1). Pull down the script

My Ubuntu 16.04

```shell
wget https://raw.githubusercontent.com/Jinva/Laravel-ubuntu16.04-init/master/deploy.sh -o deploy.sh
chmod +x deploy.sh
```

2). Config MySQL password

`vi deploy.sh` edit your password:

```shell
# Configure
MYSQL_ROOT_PASSWORD="{{--Your Password--}}"
MYSQL_NORMAL_USER="estuser"
MYSQL_NORMAL_USER_PASSWORD="{{--Your Password--}}"
```

3). Start install

Run the shell script:

```
./deploy.sh
```

> Note: You must run as `root`.

It will finish installation with this message:

```
--
--
It's Done.
Mysql Root Password: xxx你的密码xxx
Mysql Normal User: estuser
Mysql Normal User Password: xxx你的密码xxx
--
--
```

## After installation

### 1. Web root permission

Nginx using `www` user, in order to have a correct permission, you should change the owner of the directory:

```
cd /data/www/{YOU PROJECT FOLDER NAME}
chown www:www -R ./
```

### 2. Add a site

Here is a Nginx config file template for Laravel Project, you should put it at `/etc/nginx/sites-enabled` folder.

For example `/etc/nginx/sites-enabled/google.com`:

```
server {
    listen 80;
    server_name {{---YOU-DOMAIN-NAME---}};
    root "{{---YOU-PROJECT-FOLDER---}}";

    index index.html index.htm index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    access_log /data/log/nginx/{{---YOU-PROJECT-NAME---}}-access.log;
    error_log  /data/log/nginx/{{---YOU-PROJECT-NAME---}}-error.log error;

    sendfile off;

    client_max_body_size 100m;

    include fastcgi.conf;

    location ~ /\.ht {
        deny all;
    }

    location ~ \.php$ {
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}
```

Restart Nginx when you done:

```
service nginx restart
```