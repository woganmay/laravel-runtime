FROM php:8.3-cli

WORKDIR /var/www/html

# Install the same modules as the App
RUN apt-get update && \
    apt-get install -y libpq-dev libzip-dev libjpeg-dev libpng-dev libwebp-dev libfreetype-dev libcurl4-openssl-dev libxml2-dev libonig-dev libssh2-1-dev supervisor openssh-client && \
    pecl install redis-6.0.2 ssh2-1.4.1 && \
    docker-php-ext-configure gd --with-jpeg --with-webp --with-freetype && \
    docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql && \
    docker-php-ext-install pdo_pgsql zip pcntl bcmath curl dom mbstring pdo_mysql mysqli && \
    docker-php-ext-install gd && \
    docker-php-ext-enable redis bcmath curl dom mbstring ssh2 && \
    rm -rf /var/lib/apt/lists/*

# Add a config to run php artisan horizon against the mounted source code and keep that alive
COPY ./supervisord.conf /etc/supervisor/supervisord.conf

# Create a non-root runtime user with group 33 (apache's www-data group)
RUN groupadd --force -g 33 runtime
RUN useradd -ms /bin/bash --no-user-group -g 33 -u 1337 runtime

# Run supervisor on start
CMD ["/usr/bin/supervisord"]
