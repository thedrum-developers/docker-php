# Supported tags and respective `Dockerfile` links
 - `7.3-fpm`, `7-fpm`, `latest` (*[7.3/fpm/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/7.3/fpm/Dockerfile)*)
 - `7.3-fpm-dev` (*[7.3/fpm/dev/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/7.3/fpm/dev/Dockerfile)*)
 - `7.2-fpm` (*[7.2/fpm/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/7.2/fpm/Dockerfile)*)
 - `7.2-fpm-dev` (*[7.2/fpm/dev/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/7.2/fpm/dev/Dockerfile)*)
 - `7.2-apache-dev` (*[7.2/apache/dev/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/7.2/apache/dev/Dockerfile)*)
 - `7.1-fpm` (*[7.1/fpm/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/7.1/fpm/Dockerfile)*)
 - `7.1-fpm-dev` (*[7.1/fpm/dev/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/7.1/fpm/dev/Dockerfile)*)
 - `7.0-fpm` (*[7.0/fpm/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/7.0/fpm/Dockerfile)*)
 - `7.0-fpm-dev` (*[7.0/fpm/dev/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/7.0/fpm/dev/Dockerfile)*)
 - `5.6-fpm` (*[5.6/fpm/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/5.6/fpm/Dockerfile)*)
 - `5.6-fpm-dev` (*[5.6/fpm/dev/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/5.6/fpm/dev/Dockerfile)*)
 - `5.6-apache` (*[5.6/apache/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/5.6/apache/Dockerfile)*)
 - `5.6-apache-dev` (*[5.6/apache/dev/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/5.6/apache/dev/Dockerfile)*)
 - `5.5-apache` (*[5.5/apache/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/5.5/apache/Dockerfile)*)
 - `5.5-apache-dev` (*[5.5/apache/dev/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/5.5/apache/dev/Dockerfile)*)
 - `5.3-apache` (*[5.3/apache/dev/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/5.3/apache/Dockerfile)*)

# Example Docker Compose Configuration

The following is a basic example configuration for using The Drum PHP Docker images alongside Xdebug.

It uses [Compose's environment variable substitution](https://docs.docker.com/compose/environment-variables/)
to specify configuration which changes on a per-machine basis.

### .env

Create a `.env` file specifying the remote host to which Xdebug will connect;
`ifconfig | grep eth0 -A 1 | grep inet | cut -d":" -f2 | cut -d" " -f1`
should give you the necessary IP address.

```
XDEBUG_REMOTE_HOST=172.16.0.123
```

### docker-compose.yml

```
version: '2'

services:
    nginx:
        image: nginx:latest
        ports:
          - 80:80
        volumes:
          - /var/www/my-app:/var/www/my-app
          - /data/docker-config/nginx/sites-enabled/nginx.conf:/etc/nginx/nginx.conf
          - /data/docker-config/nginx/sites-enabled/example.conf:/etc/nginx/sites-enabled/example.conf
        depends_on:
          - php

    php:
        image: thedrum/php:7-fpm-dev
        environment:
          XDEBUG_CONFIG: remote_host=${XDEBUG_REMOTE_HOST}
        volumes:
          - /var/www/my-app:/var/www/my-app
          - /data/docker-config/php/xdebug.ini:/usr/local/etc/php/conf.d/xdebug.ini
          - /data/docker-config/php/opcache.ini:/usr/local/etc/php/conf.d/opcache.ini
```

### nginx.conf

```
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

    keepalive_timeout  65;
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*.conf;
}
```

### example.conf

This is a modified version of [Symfony's suggested nginx config](http://symfony.com/doc/current/setup/web_server_configuration.html#nginx).

```
server {
        listen 80;

        root /var/www/my-app;
        index index.php;

        server_name example.dev;

        access_log /var/log/nginx/example.dev.access.log;
        error_log /var/log/nginx/example.dev.error.log;

        location / {
            try_files $uri /index.php$is_args$args;
        }

        location ~ ^/index\.php(/|$) {
            fastcgi_pass php:9000;
            fastcgi_split_path_info ^(.+\.php)(/.*)$;
            fastcgi_read_timeout 300;
            include fastcgi_params;

            fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
            fastcgi_param DOCUMENT_ROOT $realpath_root;

            fastcgi_buffers 16 16k;
            fastcgi_buffer_size 32k;
            fastcgi_ignore_client_abort on;

            internal;
        }

        location ~ \.php$ {
            return 404;
        }
}

```

### xdebug.ini

```
xdebug.remote_enable = 1
xdebug.remote_autostart = 0
```

### opcache.ini

```
opcache.memory_consumption=192
opcache.interned_strings_buffer=16
opcache.max_accelerated_files=32531
opcache.revalidate_freq=0
opcache.fast_shutdown=1
opcache.enable_cli=1
opcache.enable=1
```
 
