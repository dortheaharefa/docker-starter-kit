FROM php:8.3-apache

# paket sistem
RUN apt-get update && apt-get install -y --no-install-recommends \
        git unzip libzip-dev libpng-dev libjpeg-dev libfreetype6-dev \
        libicu-dev libonig-dev libxml2-dev \
    && rm -rf /var/lib/apt/lists/*

# ekstensi PHP yang dibutuhkan Laravel
RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j"$(nproc)" \
        pdo_mysql mysqli mbstring zip intl gd bcmath exif pcntl

# Apache: docroot ke folder public
RUN a2enmod rewrite headers
COPY docker/apache/vhost.conf /etc/apache2/sites-available/000-default.conf

COPY docker/php/php.ini /usr/local/etc/php/conf.d/99-custom.ini

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/html
