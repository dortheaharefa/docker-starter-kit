# Versi PHP bisa diganti dari .env: PHP_VERSION=7.4 / 8.0 / 8.1 / 8.2 / 8.3
ARG PHP_VERSION=8.2
FROM php:${PHP_VERSION}-apache

# folder yang dijadikan docroot
#   CodeIgniter 4 -> /var/www/html/public
#   PHP native    -> /var/www/html
ARG DOCROOT=/var/www/html/public

RUN apt-get update && apt-get install -y --no-install-recommends \
        git unzip libzip-dev libpng-dev libjpeg-dev libfreetype6-dev \
        libicu-dev libonig-dev libcurl4-openssl-dev \
    && rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j"$(nproc)" \
        mysqli pdo_mysql mbstring intl gd zip curl exif

RUN a2enmod rewrite headers
COPY docker/apache/vhost.conf /etc/apache2/sites-available/000-default.conf
RUN sed -i "s#__DOCROOT__#${DOCROOT}#g" /etc/apache2/sites-available/000-default.conf

COPY docker/php/php.ini /usr/local/etc/php/conf.d/99-custom.ini

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/html
