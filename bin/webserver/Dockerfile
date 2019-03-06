FROM php:5.6-apache

# Update and upgrade the system
RUN apt-get update && apt-get upgrade -y --no-install-recommends apt-utils

# Install important libraries
RUN apt-get update && apt-get install -y \
    build-essential \
    curl \
    git \
    libcurl3 \
    libzip-dev \
    libsqlite3-dev \
    zlib1g-dev\
 && rm -rf /var/lib/apt/lists/*

# Installs sendmail
RUN apt-get update && apt-get -q -y install ssmtp mailutils && rm -rf /var/lib/apt/lists/*

# set up sendmail config
RUN echo "hostname=localhost.localdomain" > /etc/ssmtp/ssmtp.conf
RUN echo "root=<youremail@domain.co.uk>" >> /etc/ssmtp/ssmtp.conf
RUN echo "mailhub=maildev" >> /etc/ssmtp/ssmtp.conf

# Set up php sendmail config
RUN echo "sendmail_path=sendmail -i -t" >> /usr/local/etc/php/conf.d/php-sendmail.ini

# Fully qualified domain name configuration for sendmail on localhost.
RUN echo "localhost localhost.localdomain" >> /etc/hosts

# Memcached
RUN apt-get update \
        && buildDeps=" \
                git \
                libmemcached-dev \
                zlib1g-dev \
        " \
        && doNotUninstall=" \
                libmemcached11 \
                libmemcachedutil2 \
        " \
        && apt-get install -y $buildDeps --no-install-recommends \
        && rm -r /var/lib/apt/lists/* \
        \
        && docker-php-source extract \
        && git clone --branch REL2_0 https://github.com/php-memcached-dev/php-memcached /usr/src/php/ext/memcached/ \
        && docker-php-ext-install memcached \
        \
        && docker-php-source delete \
        && apt-mark manual $doNotUninstall \
        && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false $buildDeps

# Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# PECL Extensions xdebug | imagick
RUN apt-get update && apt-get install -y \
    libmagickwand-dev --no-install-recommends \
    && pecl install xdebug-2.5.5 \
    && pecl install imagick-3.4.3 \
    && docker-php-ext-enable xdebug imagick

# Other PHP5 Extensions
RUN apt-get update && apt-get install -y \
    && docker-php-ext-install bz2 \
    && docker-php-ext-install bcmath \
    && docker-php-ext-install calendar \
    && docker-php-ext-install exif \
    && docker-php-ext-install gettext \
    && docker-php-ext-install mysqli \
    && docker-php-ext-install zip

# Enable apache modules
RUN a2enmod rewrite headers
