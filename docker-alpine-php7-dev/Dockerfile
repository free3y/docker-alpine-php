FROM alpine:edge

Maintainer YongYong Ye ( http://github.com/yeyongyong/ )

COPY repositories /etc/apk/

RUN apk update && apk upgrade

#basic tools
RUN apk add wget vim bash git tar curl grep zlib make libxml2 readline \
    freetype openssl libjpeg-turbo libpng libmcrypt libwebp icu

RUN buildDeps=" build-base re2c file readline-dev autoconf binutils bison \
        libxml2-dev curl-dev freetype-dev openssl-dev \
        libjpeg-turbo-dev libpng-dev libwebp-dev libmcrypt-dev \
        gmp-dev icu-dev libmemcached-dev linux-headers" \
    && apk --update add $buildDeps

RUN apk add \
	libmemcached-dev \
	php7-dev \
	php7 \
	php7-pear \
	php7-opcache

RUN ln -s /usr/bin/php7 /usr/bin/php \
&& ln -s /usr/bin/php-config7 /usr/bin/php-config \
&& ln -s /usr/bin/phpize7 /usr/bin/phpize \
&& rm -rf /var/cache/apk/*

#special patch for alpine linux-kernel have some strange incompatibility
RUN sed -i "s/struct sigaction {/#ifndef __sighandler_t \ntypedef void (*__sighandler_t)(int);\n#endif\nstruct sigaction\n{/g" /usr/include/signal.h
RUN sed -i "s/union {void (*sa_handler)(int)/__sighandler_t sa_handler/g" /usr/include/signal.h

#let pecl use /etc/php/php.ini by find the pecl and patch...
RUN sed -i "s/ -n / /" `which pecl`

#install php-swoole
RUN pecl install swoole
RUN pecl install xdebug

RUN cd /tmp \ 
   && git clone https://github.com/php-memcached-dev/php-memcached.git \
   && cd php-memcached \
   && git checkout php7 \
   && phpize \
   && ./configure --disable-memcached-sasl \
   && make \
   && make test \
   && make install

RUN cd /tmp \
   && git clone https://github.com/phpredis/phpredis.git \
   && cd phpredis \
   && git checkout php7 \
   && phpize \
   && ./configure \
   && make \
   && make test \
   && make install