FROM ubuntu:12.04

ENV PHP_INI_DIR /etc/php5

VOLUME ["/var/www"]

RUN apt-get update && \
    apt-get install -y \
        build-essential \
        curl \
        apache2 \
        php5 \
        php5-cli \
        libapache2-mod-php5 \
        php5-gd \
        php5-mysql \
        php-pear

RUN a2enmod rewrite

EXPOSE 80

CMD ["/usr/local/bin/run"]
