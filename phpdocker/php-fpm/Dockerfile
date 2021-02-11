FROM php:7.1.9-fpm

WORKDIR "/application"

# Fix debconf warnings upon build
ARG DEBIAN_FRONTEND=noninteractive

# Install selected extensions and other stuff
RUN apt-get update \
  && docker-php-ext-install pdo_mysql mysqli mbstring exif

RUN apt-get update \
  && apt-get install -y libmemcached-dev zlib1g-dev libpng-dev jpegoptim optipng libmagickwand-dev git xvfb libfontconfig wkhtmltopdf \
  && pecl install imagick memcached-3.0.3 xdebug \
  && docker-php-ext-enable memcached opcache imagick

# GD Installations
RUN apt-get install -y \
        wget \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng12-dev \
     && docker-php-ext-configure gd \
          --enable-gd-native-ttf \
          --with-freetype-dir=/usr/include/freetype2 \
          --with-png-dir=/usr/include \
          --with-jpeg-dir=/usr/include \
    && docker-php-ext-install gd \
    && docker-php-ext-enable gd

# XDEBUG Installation
RUN pecl install xdebug; \
    docker-php-ext-enable xdebug; \
    echo "error_reporting = E_ALL" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini; \
    echo "display_startup_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini; \
    echo "display_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini; \
    echo "xdebug.remote_handler=dbgp"  >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini; \
    echo "xdebug.remote_autostart=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini; \
    echo "xdebug.remote_enable=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini; \
    echo "xdebug.remote_connect_back=0" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini; \
    echo "xdebug.remote_port=9000" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini; \
    echo "xdebug.remote_host=192.168.100.101" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini; \
    echo "xdebug.max_nesting_level=1500" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini;

# Composer scripts
WORKDIR /usr/local/bin

ENV COMPOSER_ALLOW_SUPERUSER 1
COPY composer.sh /root
RUN chmod 755 /root/composer.sh
RUN /root/composer.sh
RUN php composer-setup.php
RUN mv composer.phar /usr/local/bin/composer
RUN rm composer-setup.php

WORKDIR /usr/local/bin

COPY run.sh /root
RUN chmod 755 /root/run.sh

CMD ["/root/run.sh"]