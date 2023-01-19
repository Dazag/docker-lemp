FROM php:8-fpm

ARG APP_USER_ID
ARG APP_GROUP_ID

ENV APP_USER_ID "$APP_USER_ID"
ENV APP_GROUP_ID "$APP_GROUP_ID"

# Add common console utilities/packages
RUN apt-get update && \
    apt-get install -y --no-install-recommends apt-utils git zip libzip-dev unzip nano wget zsh

# Add npm
#RUN curl -sL https://deb.nodesource.com/setup_16.x  | bash -
#RUN apt-get -y install nodejs

# MySQL server packages
RUN apt-get install -y default-mysql-client \
    && docker-php-ext-install pdo pdo_mysql

# Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# php-memcached PECL
RUN apt-get install -y libmemcached-dev zlib1g-dev \
    && pecl install memcached \
    && docker-php-ext-enable memcached

# Imagick PECL
RUN apt-get install -y libmagickwand-dev \
    && pecl install imagick \
    && docker-php-ext-enable imagick

# Configure GD
RUN apt-get install -y libjpeg-dev libfreetype6-dev libpng-dev \
    && docker-php-ext-configure gd --with-jpeg=/usr/include/ --with-freetype=/usr/include/ \
    && docker-php-ext-install gd

# Install xdebug
RUN pecl install xdebug \
    && docker-php-ext-enable xdebug

# Install other PHP extensions
RUN docker-php-ext-install zip opcache

#Add custom php.ini
COPY php.ini /usr/local/etc/php/conf.d/
COPY xdebug.ini /usr/local/etc/php/conf.d/

# App related actions from here #

# Assign 'app' user to docker container with your user ID and group ID in .env file
RUN groupadd -r app -g $APP_GROUP_ID && useradd -u $APP_USER_ID -r -g app -m -l -d /home/app -s /sbin/nologin -c "App user" app
USER app

# ZSH
RUN sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.1.3/zsh-in-docker.sh)" -- \
    -t https://github.com/romkatv/powerlevel10k \
    -p git \
    -p history \
    -p git-flow  \
    -p composer \
    -p npm \
    -p command-not-found \
    -p https://github.com/zsh-users/zsh-autosuggestions \
    -p https://github.com/zsh-users/zsh-completions

COPY zsh/.zshrc $HOME
COPY zsh/.p10k.zsh $HOME
