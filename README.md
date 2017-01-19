# Supported tags and respective `Dockerfile` links
 - `7-fpm`, `latest` (*[7/fpm/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/7/fpm/Dockerfile)*)
 - `7-fpm-dev` (*[7/fpm/dev/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/7/fpm/dev/Dockerfile)*)
 - `5.6-fpm` (*[5.6/fpm/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/5.6/fpm/Dockerfile)*)
 - `5.6-fpm-dev` (*[5.6/fpm/dev/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/5.6/fpm/dev/Dockerfile)*)
 - `5.6-apache` (*[5.6/apache/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/5.6/apache/Dockerfile)*)
 - `5.6-apache-dev` (*[5.6/apache/dev/Dockerfile](https://github.com/thedrum-developers/docker-php/blob/master/5.6/apache/dev/Dockerfile)*)

# Example Docker Compose Configuration

The following is a basic example configuration for using The Drum PHP docker images alongside Xdebug.

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
        volumes:
          - /var/www/my-app:/var/www/my-app
          - /data/docker-config/nginx:/etc/nginx
        depends_on:
          - php

    php:
        image: thedrum/php:7-fpm-dev
        environment:
          XDEBUG_CONFIG: remote_host=${XDEBUG_REMOTE_HOST}
        volumes:
          - /var/www/my-app:/var/www/my-app
          - /data/docker-config/php/xdebug.ini:/usr/local/etc/php/conf.d/xdebug.ini
```

### xdebug.ini

```
xdebug.remote_enable = 1
xdebug.remote_autostart = 0
```