FROM php:8.3-apache

WORKDIR /var/www/html

RUN mkdir -p /usr/local/lib/php/extensions/no-debug-non-zts-20230831/modules

# Install the PostgreSQL PDO extension
# Docs: https://github.com/mlocati/docker-php-extension-installer
RUN apt-get update && \
    apt-get install -y libpq-dev libzip-dev libcurl4-openssl-dev libxml2-dev libonig-dev libjpeg-dev libpng-dev libwebp-dev libfreetype-dev && \
    pecl install redis-6.0.2 && \
    docker-php-ext-configure gd --with-jpeg --with-webp --with-freetype && \
    docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql && \
    docker-php-ext-install pgsql pdo pdo_pgsql zip pcntl bcmath curl dom mbstring && \
    docker-php-ext-install gd && \
    docker-php-ext-enable redis bcmath curl dom mbstring && \
    rm -rf /var/lib/apt/lists/*

# Install the latest Composer
# TODO: Is this necessary?
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
    php -r "if (hash_file('sha384', 'composer-setup.php') === 'dac665fdc30fdd8ec78b38b9800061b4150413ff2e3b6f88543c636f7cd84f6db9189d43a81e5503cda447da73c7e5b6') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" && \
    php composer-setup.php --install-dir=/usr/bin/ --filename=composer && \
    php -r "unlink('composer-setup.php');"

# Configure the default site
COPY ./000-default.conf /etc/apache2/sites-available/000-default.conf
RUN a2enmod rewrite

# Create a non-root runtime user with group 33 (apache's www-data group)
RUN groupadd --force -g 33 runtime
RUN useradd -ms /bin/bash --no-user-group -g 33 -u 1337 runtime