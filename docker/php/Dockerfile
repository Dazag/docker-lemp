FROM php:7.4-fpm

# Add nano for debugging
RUN apt-get update && \
    apt-get install -y --no-install-recommends apt-utils git zip libzip-dev unzip nano

# GD extension dependency
RUN apt-get install -y libpng-dev

# MySQL server
RUN apt-get install -y default-mysql-client

# Composer
RUN curl --silent --show-error https://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer && \
    chmod +x /usr/local/bin/composer && \
    composer --version

# PHPUnit
RUN curl -fsSL https://phar.phpunit.de/phpunit-6.5.phar -o phpunit-6.5.phar && \
    mv phpunit-6.5.phar /usr/local/bin/phpunit && \
    chmod +x /usr/local/bin/phpunit && \
    phpunit --version

# php-memcached
RUN apt-get install -y libmemcached-dev zlib1g-dev \
    && pecl install memcached \
    && docker-php-ext-enable memcached

# Imagick
RUN apt-get install -y libmagickwand-dev \
    && pecl install imagick \
    && docker-php-ext-enable imagick

# Configure GD
RUN docker-php-ext-configure gd --with-jpeg=/usr/include/ --with-freetype=/usr/include/

# Install PHP extensions
RUN docker-php-ext-install pdo pdo_mysql zip opcache gd

# Install xdebug
RUN pecl install xdebug \
    && docker-php-ext-enable xdebug

#Add custom php.ini
COPY php.ini /usr/local/etc/php/conf.d/
COPY xdebug.ini /usr/local/etc/php/conf.d/

#Add 'app' user to docker container
RUN useradd -rm -d /home/app -s /bin/bash -g root -G sudo -u 1000 app
USER app
