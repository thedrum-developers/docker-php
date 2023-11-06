FROM amazonlinux:2

RUN yum install shadow-utils -y

RUN amazon-linux-extras install php8.0

RUN amazon-linux-extras install epel

RUN yum clean metadata
RUN yum install tar -y
RUN yum install php-{cli,curl,mbstring,gd,mysqlnd,json,xml,fpm,intl,zip,opcache} -y

RUN yum install supervisor -y

RUN version=$(php -r "echo PHP_MAJOR_VERSION.PHP_MINOR_VERSION;") \
    && architecture=$(case $(uname -m) in i386 | i686 | x86) echo "i386" ;; x86_64 | amd64) echo "amd64" ;; aarch64 | arm64 | armv8) echo "arm64" ;; *) echo "amd64" ;; esac) \
    && curl -A "Docker" -o /tmp/blackfire-probe.tar.gz -D - -L -s https://blackfire.io/api/v1/releases/probe/php/linux/$architecture/$version \
    && mkdir -p /tmp/blackfire \
    && tar zxpf /tmp/blackfire-probe.tar.gz -C /tmp/blackfire \
    && mv /tmp/blackfire/blackfire-*.so $(php -r "echo ini_get ('extension_dir');")/blackfire.so \
    && printf "extension=blackfire.so\nblackfire.agent_socket=tcp://blackfire:8307\n" > /etc/php.d/blackfire.ini \
    && rm -rf /tmp/blackfire /tmp/blackfire-probe.tar.gz

RUN php --version

RUN groupadd -g 1001 www-data && useradd -u 1001 -r -s /usr/sbin/nologin -g www-data -d /var/www www-data

COPY docker-php-entrypoint /bin/

RUN set -xe \
    chown -R www-data:www-data . \
    && chmod +x /bin/docker-php-entrypoint

ENTRYPOINT ["docker-php-entrypoint"]

WORKDIR /var/www/html

STOPSIGNAL SIGQUIT

EXPOSE 9000
CMD ["php-fpm"]
