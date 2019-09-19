ARG PHP_VERSION=7.2

FROM shanestevenlei/php:${PHP_VERSION}-fpm-alpine

LABEL maintainer="StevenLei <shanestevenlei@gmail.com>"

ARG INSTALL_XDEBUG=false
ARG INSTALL_BCMATH=false
ARG INSTALL_MYSQLI=false
ARG INSTALL_OPCACHE=false
ARG INSTALL_PHPREDIS=false
ARG INSTALL_MEMCACHED=false
ARG INSTALL_APCU=false
ARG INSTALL_EXIF=false
ARG INSTALL_XSL=false
ARG INSTALL_SWOOLE=false
ARG INSTALL_XHPROF=false


# Copy xdebug configuration for remote debugging
COPY ./config/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

RUN sed -i "s/xdebug.remote_autostart=0/xdebug.remote_autostart=1/" /usr/local/etc/php/conf.d/xdebug.ini && \
    sed -i "s/xdebug.remote_enable=0/xdebug.remote_enable=1/" /usr/local/etc/php/conf.d/xdebug.ini && \
    sed -i "s/xdebug.cli_color=0/xdebug.cli_color=1/" /usr/local/etc/php/conf.d/xdebug.ini

RUN apk --no-cache add autoconf g++ make libzip-dev zip unzip \
    && docker-php-ext-configure zip --with-libzip \
    && docker-php-ext-install zip \
    && php -m | grep -q 'zip' \
    # Install the xdebug extension
    && if [ ${INSTALL_XDEBUG} = true ]; then \
        if [ $(php -r "echo PHP_MAJOR_VERSION;") = "5" ]; then \
            pecl install xdebug-2.5.5; \
        else \
            pecl install xdebug; \
        fi && \
        docker-php-ext-enable xdebug \
    ;fi \
    # Install the bcmath extension
    && if [ ${INSTALL_BCMATH} = true ]; then \
        docker-php-ext-install bcmath \
    ;fi \
    # Install the mysqli extension
    && if [ ${INSTALL_MYSQLI} = true ]; then \
        docker-php-ext-install mysqli \
    ;fi \
    # Install the opcache extension
    && if [ ${INSTALL_OPCACHE} = true ]; then \
        docker-php-ext-install opcache \
    ;fi \
    # Enable Exif PHP extentions requirements
    && if [ ${INSTALL_EXIF} = true ]; then \
        docker-php-ext-install exif \
    ;fi \
    # Install Php Redis Extension
    && if [ ${INSTALL_PHPREDIS} = true ]; then \
        printf "\n" | pecl install -o -f redis \
        &&  rm -rf /tmp/pear \
        &&  docker-php-ext-enable redis \
    ;fi \
    # Install the php memcached extension
    && if [ ${INSTALL_MEMCACHED} = true ]; then \
        if [ $(php -r "echo PHP_MAJOR_VERSION;") = "5" ]; then \
            curl -L -o /tmp/memcached.tar.gz "https://github.com/php-memcached-dev/php-memcached/archive/2.2.0.tar.gz"; \
        else \
            curl -L -o /tmp/memcached.tar.gz "https://github.com/php-memcached-dev/php-memcached/archive/master.tar.gz"; \
        fi \
        && mkdir -p memcached \
        && tar -C memcached -zxvf /tmp/memcached.tar.gz --strip 1 \
        && ( \
            cd memcached \
            && phpize \
            && ./configure \
            && make -j$(nproc) \
            && make install \
        ) \
        && rm -r memcached \
        && rm /tmp/memcached.tar.gz \
        && docker-php-ext-enable memcached \
    ;fi \
    # Install the php apcu extension
    && if [ ${INSTALL_APCU} = true ]; then \
        if [ $(php -r "echo PHP_MAJOR_VERSION;") = "5" ]; then \
            pecl install -a apcu-4.0.11; \
        else \
            pecl install apcu; \
        fi && \
        docker-php-ext-enable apcu \
    ;fi \
    # Install the xsl extension
    && if [ ${INSTALL_XSL} = true ]; then \
        apk --no-cache add libxslt-dev && \
        docker-php-ext-install xsl \
    ;fi \
    # Install Php Swoole Extension
    && if [ ${INSTALL_SWOOLE} = true ]; then \
        if [ $(php -r "echo PHP_MAJOR_VERSION;") = "5" ]; then \
            pecl install swoole-2.0.10; \
        else \
            if [ $(php -r "echo PHP_MINOR_VERSION;") = "0" ]; then \
                pecl install swoole-2.2.0; \
            else \
                pecl install swoole; \
            fi \
        fi && \
        docker-php-ext-enable swoole \
        && php -m | grep -q 'swoole' \
    ;fi \
    # Install the php xhprof extension 
    && if [ ${INSTALL_XHPROF} = true ]; then \
        if [ $(php -r "echo PHP_MAJOR_VERSION;") = 7 ]; then \
          curl -L -o /tmp/xhprof.tar.gz "https://github.com/tideways/php-xhprof-extension/archive/v4.1.7.tar.gz"; \
        else \
          curl -L -o /tmp/xhprof.tar.gz "https://codeload.github.com/phacility/xhprof/tar.gz/master"; \
        fi \
        && mkdir -p xhprof \
        && tar -C xhprof -zxvf /tmp/xhprof.tar.gz --strip 1 \
        && ( \
            cd xhprof \
            && phpize \
            && ./configure \
            && make \
            && make install \
        ) \
        && rm -r xhprof \
        && rm /tmp/xhprof.tar.gz \
    ;fi

RUN rm -rf /var/cache/apk/*

WORKDIR /var/www

CMD ["php-fpm"]

EXPOSE 9000