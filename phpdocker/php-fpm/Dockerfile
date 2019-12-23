FROM php:7.1.9-fpm

WORKDIR "/application"

# Fix debconf warnings upon build
ARG DEBIAN_FRONTEND=noninteractive

# Install selected extensions and other stuff
RUN apt-get update \
  && docker-php-ext-install pdo_mysql mysqli mbstring

RUN apt-get update \
  && apt-get install -y libmemcached-dev zlib1g-dev libpng-dev jpegoptim optipng libmagickwand-dev \
  && pecl install imagick memcached-3.0.3 xdebug \
  && docker-php-ext-enable memcached opcache imagick

RUN apt-get install -y \
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

ENV COMPOSER_ALLOW_SUPERUSER 1

COPY run.sh /root
RUN chmod 755 /root/run.sh

WORKDIR /usr/local/bin
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
&& php -r "if (hash_file('sha384', 'composer-setup.php') === 'baf1608c33254d00611ac1705c1d9958c817a1a33bce370c0595974b342601bd80b92a3f46067da89e3b06bff421f182') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
&& php composer-setup.php \
&& php -r "unlink('composer-setup.php');"

CMD ["/root/run.sh"]