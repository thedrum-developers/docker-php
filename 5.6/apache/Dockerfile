FROM php:5.6-apache

RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
    && docker-php-ext-install pdo pdo_mysql \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd exif

RUN apt-get install -y git zlib1g-dev libmcrypt-dev nano vim --no-install-recommends \
    && apt-get clean \
    && docker-php-ext-install mcrypt zip \
    && a2enmod rewrite \
    && a2enmod proxy \
    && a2enmod proxy_http \
    && a2enmod headers \
    && a2enmod ssl
