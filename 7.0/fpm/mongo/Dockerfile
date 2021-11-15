FROM php:7.1-fpm

RUN apt-get update

RUN apt-get install -y autoconf g++ make openssl libssl-dev libcurl4-openssl-dev libcurl4-openssl-dev pkg-config libsasl2-dev \
    && pecl install mongodb \
    && docker-php-ext-enable mongodb
