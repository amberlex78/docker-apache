FROM php:5.6-apache

#  TIMEZONE AND LOCALE
ENV TZ=Europe/Kiev
RUN apt-get update && apt-get install -y apt-utils locales && \
    echo $TZ > /etc/timezone && \
    dpkg-reconfigure -f noninteractive tzdata && \
    echo date.timezone = $TZ > /usr/local/etc/php/conf.d/docker-php-ext-timezone.ini && \
    sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen && \
    sed -i -e 's/# ru_RU.UTF-8 UTF-8/ru_RU.UTF-8 UTF-8/' /etc/locale.gen && \
    echo 'LANG="en_US.UTF-8"' > /etc/default/locale && \
    dpkg-reconfigure --frontend=noninteractive locales && \
    update-locale LANG=en_US.UTF-8

ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8
#ENV LC_ALL en_US.UTF-8

# SYSTEM SETUP
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        libgmp-dev \
        libxml2-dev \
        libxslt-dev \
        zlib1g-dev \
        libncurses5-dev \
        libldap2-dev \
        libicu-dev \
        libmemcached-dev \
        libcurl4-openssl-dev \
        libssl-dev \
        libsqlite3-dev \
        exiftool \
        curl \
        ssmtp \
        mysql-client \
        git \
        wget && \
    rm -rf /var/lib/apt/lists/* && \
    wget https://getcomposer.org/download/1.10.22/composer.phar -O /usr/local/bin/composer && \
    chmod a+rx /usr/local/bin/composer

# PHP EXTENSIONS
RUN docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd && \
    docker-php-ext-configure mysql --with-mysql=mysqlnd && \
    docker-php-ext-configure mysqli --with-mysqli=mysqlnd && \
    docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-png-dir=/usr/include/ --with-jpeg-dir=/usr/include

RUN docker-php-ext-install \
    pdo_mysql \
    mysql \
    mysqli \
    gd \
    bcmath \
    exif \
    gettext \
    intl \
    mcrypt \
    opcache \
    pcntl \
    soap \
    sockets \
    zip

# PHP
RUN cp $PHP_INI_DIR/php.ini-development $PHP_INI_DIR/php.ini
COPY ./php/conf.d $PHP_INI_DIR/conf.d

# PHP IONCUBE LOADER
COPY ./vendor/ioncube/ioncube_loader_lin_5.6.so /tmp/ioncube/ioncube_loader_lin_5.6.so
RUN mv /tmp/ioncube/ioncube_loader_lin_5.6.so `php-config --extension-dir`
RUN docker-php-ext-enable ioncube_loader_lin_5.6.so

# APACHE SETUP
RUN a2enmod rewrite && \
    service apache2 restart

COPY 000-default.conf /etc/apache2/sites-available/000-default.conf

COPY . /var/www/html/
WORKDIR /var/www/html/

CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]