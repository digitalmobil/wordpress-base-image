FROM alpine:3.5

ENV PHP_INI_DIR /etc/php5
ENV HTTPD_DOCUMENT_ROOT /var/www/localhost/htdocs

# ensure www-data user exists
RUN set -x \
    && addgroup -g 82 -S www-data \
    && adduser -u 82 -D -S -G www-data www-data
# 82 is the standard uid/gid for "www-data" in Alpine
# http://git.alpinelinux.org/cgit/aports/tree/main/apache2/apache2.pre-install?h=v3.3.2
# http://git.alpinelinux.org/cgit/aports/tree/main/lighttpd/lighttpd.pre-install?h=v3.3.2
# http://git.alpinelinux.org/cgit/aports/tree/main/nginx-initscripts/nginx-initscripts.pre-install?h=v3.3.2

RUN set -xe; \
    apk add --no-cache --virtual .image-runtime-deps \
        bash \
        sed \
        unzip \
        zip \
        curl \
        tar \
        gzip \
        bzip2 \
        xz 

RUN set -xe; \
    apk add --no-cache --virtual .wordpress-runtime-deps \
        apache2 \
        apache2-utils \
        php5-apache2 \
        php5 \
        php5-mysqli \
        php5-pdo \
        php5-pdo_mysql \
        php5-mcrypt \
        php5-gd \
        php5-intl \
        php5-opcache \
        php5-soap \
    && mkdir -p /run/apache2 \
    && chown -R www-data:www-data $HTTPD_DOCUMENT_ROOT \
    && chmod g+ws $HTTPD_DOCUMENT_ROOT \
    && rm $HTTPD_DOCUMENT_ROOT/index.html \
    && sed -ri \
        -e 's!^(\s*CustomLog)\s+\S+!\1 /proc/self/fd/1!g' \
        -e 's!^(\s*ErrorLog)\s+\S+!\1 /proc/self/fd/2!g' \
        -e 's!^#LoadModule rewrite_module!LoadModule rewrite_module!' \
        "/etc/apache2/httpd.conf" 

RUN { \
        echo 'date.timezone = "Europe/Berlin"';     \
        echo 'zlib.output_compression = On';        \
        echo 'zlib.output_compression_level = 6';   \  
        echo 'memory_limit = 128M';                 \ 
        echo 'max_execution_time = 90';             \ 
        echo 'upload_max_filesize = 16M';           \
        echo 'post_max_size = 16M';                 \
        echo 'log_errors = On';                     \
        echo 'error_log = "/var/www/php.log"';      \
    } > $PHP_INI_DIR/conf.d/wordpress.ini 
    
# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
RUN { \
        echo 'opcache.memory_consumption=128'; \
        echo 'opcache.interned_strings_buffer=8'; \
        echo 'opcache.max_accelerated_files=4000'; \
        echo 'opcache.revalidate_freq=60'; \
        echo 'opcache.fast_shutdown=1'; \
        echo 'opcache.enable_cli=1'; \
    } > $PHP_INI_DIR/conf.d/opcache-recommended.ini

COPY httpd-foreground /usr/local/bin/

WORKDIR $HTTPD_DOCUMENT_ROOT

EXPOSE 80
CMD ["httpd-foreground"]

