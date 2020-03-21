FROM php:7.4-fpm
LABEL maintainer="zww"


# set timezome
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

#设置源
COPY ./sources.list /etc/apt/sources.list

#设置ORACLE变量
ENV LD_LIBRARY_PATH /usr/local/instantclient_12_2
ENV TNS_ADMIN /usr/local/instantclient_12_2
ENV ORACLE_BASE /usr/local/instantclient_12_2
ENV ORACLE_HOME /usr/local/instantclient_12_2

#安装ORACLE客户端
COPY ./pkg/instantclient-basic-linux.x64-12.2.0.1.0.zip /tmp
COPY ./pkg/instantclient-sdk-linux.x64-12.2.0.1.0.zip /tmp
COPY ./pkg/instantclient-odbc-linux.x64-12.2.0.1.0-2.zip /tmp
COPY ./pkg/instantclient-sqlplus-linux.x64-12.2.0.1.0.zip /tmp

#安装扩展 libaio->ORACLE
RUN apt-get clean && apt-get update && apt-get install -y \
        unzip\
        build-essential \
        libaio-dev \
        libaio1 \
        libmagickcore-dev \
        libmagickwand-dev \
        imagemagick \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        libmcrypt-dev \
        libzip-dev \
        && unzip /tmp/instantclient-basic-linux.x64-12.2.0.1.0.zip -d /usr/local/ \
        && unzip /tmp/instantclient-sdk-linux.x64-12.2.0.1.0.zip -d /usr/local/ \
        && unzip /tmp/instantclient-odbc-linux.x64-12.2.0.1.0-2.zip -d /usr/local/ \
        && unzip /tmp/instantclient-sqlplus-linux.x64-12.2.0.1.0.zip -d /usr/local/ \
        && ln -s /usr/local/instantclient_12_2/libclntsh.so.12.1 /usr/local/instantclient_12_2/libclntsh.so \
        && ln -s /usr/local/instantclient_12_2/libocci.so.12.1 /usr/local/instantclient_12_2/libocci.so \
        && ln -s /usr/local/instantclient_12_2/sqlplus /usr/bin/sqlplus \
        && rm -rf /usr/local/*.zip

#安装扩展
RUN docker-php-ext-configure gd \
        # && docker-php-ext-configure oci8 --with-oci=shared,instantclient,/usr/local/instantclient_12_2 \
        && docker-php-ext-configure pdo_oci --with-pdo-oci=instantclient,/usr/local/instantclient_12_2,12.2.0.1.0 \
        && echo 'shared,instantclient,/usr/local/instantclient_12_2/' | pecl install oci8 \
        && docker-php-ext-install pdo_oci \
        # && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
        && docker-php-ext-install -j$(nproc) gd \
        && docker-php-ext-install zip \
        && docker-php-ext-install pdo_mysql \
        && docker-php-ext-install mysqli \
        && docker-php-ext-install opcache \
        && docker-php-ext-install exif \
        && docker-php-ext-install sockets \
        && docker-php-ext-install gettext \
        && pecl install redis-5.1.1 \
        && pecl install imagick \
        && pecl install mcrypt \
        && pecl install swoole \
        && docker-php-ext-enable redis imagick mcrypt oci8 swoole \
        && rm -r /var/lib/apt/lists/*

# Copy extensions had downloaded
# COPY ./pkg/swoole-4.3.5.tgz /home/swoole-4.3.5.tgz
# Install PECL extensions (Swoole)
# RUN pecl install /home/swoole-4.3.5.tgz && echo "extension=swoole.so" > /usr/local/etc/php/conf.d/swoole.ini
# RUN rm -f /home/swoole-4.3.5.tgz


# Copy extensions had downloaded
# COPY ./pkg/redis.tgz /home/redis.tgz
# Install PECL extensions (Redis)
# RUN pecl install /home/redis.tgz && echo "extension=redis.so" > /usr/local/etc/php/conf.d/redis.ini
# RUN rm -f /home/redis.tgz


# Install some ?

# Install Composer
ENV COMPOSER_HOME /root/composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
ENV PATH $COMPOSER_HOME/vendor/bin:$PATH


WORKDIR /data

# Write Permission
RUN usermod -u 1000 www-data
