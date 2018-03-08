FROM thedrum/php:5.3-apache

RUN pecl install xdebug-2.2.7 && \
    touch "$PHP_INI_DIR/conf.d/xdebug.ini" && \
    echo "zend_extension=/usr/lib/php5/20090626/xdebug.so" > "$PHP_INI_DIR/conf.d/xdebug.ini"
