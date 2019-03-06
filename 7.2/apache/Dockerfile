FROM php:7.2-apache

RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        libicu-dev \
        libxml2-dev \
    && docker-php-ext-install pdo pdo_mysql \
    && docker-php-ext-install mysqli \
    && docker-php-ext-enable mysqli \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd exif \
    && docker-php-ext-install opcache \
    && docker-php-ext-install zip \
    && a2enmod rewrite \
    && a2enmod proxy_http \
    && a2enmod ssl
