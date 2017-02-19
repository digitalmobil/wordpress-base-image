FROM php:5.6-apache

ENV HTTPD_DOCUMENT_ROOT /var/www

# Enable Apache Rewrite Module
RUN a2enmod rewrite

RUN apt-get update && apt-get install -y --force-yes --no-install-recommends \
	libc-client-dev libicu-dev libmcrypt-dev libssl-dev libpng12-dev libjpeg-dev \
    unzip zip \
	&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/Users

RUN docker-php-ext-install intl mbstring mcrypt mysqli pdo pdo_mysql gd opcache

RUN { \
        echo 'date.timezone = "Europe/Berlin"';     \
        echo 'zlib.output_compression = On';        \
        echo 'zlib.output_compression_level = 6';   \  
        echo 'memory_limit = 128M';                 \ 
        echo 'max_execution_time = 90';             \ 
        echo 'upload_max_filesize = 16M';           \
        echo 'post_max_size = 16M';                 \
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

WORKDIR $HTTPD_DOCUMENT_ROOT

CMD ["apache2-foreground"]

