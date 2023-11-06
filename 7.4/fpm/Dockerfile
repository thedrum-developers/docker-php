FROM php:7.4-fpm

RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        libicu-dev \
        libxml2-dev \
        libzip-dev \
        zlib1g-dev \
        unzip \
        xvfb \
        libfontconfig \
        wkhtmltopdf \
        supervisor \
    && docker-php-ext-install pdo pdo_mysql \
    && docker-php-ext-install mysqli \
    && docker-php-ext-enable mysqli \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd exif \
    && docker-php-ext-install intl \
    && docker-php-ext-install opcache \
    && docker-php-ext-install zip \
	&& docker-php-ext-install soap

RUN pecl install apcu && \
    docker-php-ext-enable apcu --ini-name docker-php-ext-10-apcu.ini
