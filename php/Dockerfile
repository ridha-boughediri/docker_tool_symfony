FROM php:8.2-fpm

# Arguments pour la configuration Git
ARG GIT_USEREMAIL
ARG GIT_USERNAME
ENV GIT_USEREMAIL $GIT_USEREMAIL
ENV GIT_USERNAME $GIT_USERNAME

# Version de Node.js à installer
ARG NODE_VERSION=16

# Installation des dépendances et des extensions PHP nécessaires
RUN apt-get update && apt-get install -y \
    zlib1g-dev \
    g++ \
    git \
    libicu-dev \
    zip \
    libzip-dev \
    nano \
    vim \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    --no-install-recommends && \
    docker-php-ext-install intl opcache pdo pdo_mysql exif && \
    pecl install apcu && \
    docker-php-ext-enable apcu && \
    docker-php-ext-configure zip && \
    docker-php-ext-install zip && \
    docker-php-ext-configure gd --with-freetype --with-jpeg && \
    docker-php-ext-install gd && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Installation de Xdebug
RUN pecl install xdebug && \
    docker-php-ext-enable xdebug && \
    echo "xdebug.mode=debug,coverage" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo "xdebug.client_host=host.docker.internal" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# Installation de Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Installation du Symfony CLI
RUN curl -sS https://dl.cloudsmith.io/public/symfony/stable/setup.deb.sh | bash && \
    apt-get install -y symfony-cli

# Configuration Git
RUN git config --global user.email "$GIT_USEREMAIL" && \
    git config --global user.name "$GIT_USERNAME"

# Installation de Node.js et Yarn
RUN curl -sL https://deb.nodesource.com/setup_$NODE_VERSION.x | bash - && \
    curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list && \
    apt-get update && \
    apt-get install -qq -y build-essential nodejs yarn && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Copier le fichier php.ini
COPY php.ini /usr/local/etc/php/php.ini

# Copier le fichier .bashrc
COPY .bashrc /root/.bashrc

# Définir le répertoire de travail
WORKDIR /var/www/docker_sf
