FROM php:7.3-fpm

RUN apt-get update -qq && apt-get install -y \
        $PHPIZE_DEPS \
        git \
        gettext \
        wget \
        libxml2-dev

RUN pecl install xdebug

RUN pecl install -o -f redis && \
        rm -rf /tmp/pear && \
        docker-php-ext-enable xdebug redis

RUN docker-php-ext-install gettext

# 3.6.5 - from doc
# 3.8.4 - Last
ENV XL_VERSION 3.8.4

ENV PHP_VERSION 7.3.3

RUN wget http://libxl.com/download/libxl-lin-$XL_VERSION.tar.gz && \
        tar xfz libxl-lin-$XL_VERSION.tar.gz && \
        cp -p libxl-$XL_VERSION.0/lib64/libxl.so /usr/lib/libxl.so && \
        cp -r libxl-$XL_VERSION.0/include_c/ /usr/include/libxl_c && \
        rm -f libxl-lin-$XL_VERSION.tar.gz

RUN apt-get install -y \
          zlib1g-dev \
          libzip-dev \
          zip \
        && pecl install xlswriter \
        && pecl install zip \
        && docker-php-ext-enable xlswriter \
        && docker-php-ext-configure zip --with-libzip \
        && docker-php-ext-install zip

RUN git clone https://github.com/Jan-E/php_excel.git -b php7-issue241 php_excel && \
        cd php_excel && \
        phpize && \
        ./configure \
          --with-libxml-dir=/usr/include/libxml2/libxml \
          --with-libxl-incdir=/usr/include/libxl_c \
          --with-excel=shared && \
        make -j "$(nproc)" \
        && make install \
        && docker-php-ext-enable excel

RUN docker-php-ext-configure bcmath --enable-bcmath \
    && docker-php-ext-configure mysqli --with-mysqli \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-configure soap --enable-soap \
    && docker-php-ext-install -j$(nproc) \
        bcmath \
        mysqli \
        pdo_mysql \
        soap

RUN docker-php-source delete && \
        apt-get clean

COPY php.ini /etc/php7/conf.d/50-setting.ini
COPY php-fpm.conf /etc/php7/php-fpm.conf

RUN mkdir /application
RUN mkdir -p /application/static
RUN mkdir -p /application/media
COPY phpinfo.php /application/phpinfo.php
WORKDIR /application

EXPOSE 9000

CMD ["php-fpm", "-F"]
