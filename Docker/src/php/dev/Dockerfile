FROM php:7.4-fpm
ENV TZ=Asia/Tehran

RUN apt-get -y update && apt-get -y install libcurl4-openssl-dev wget zip zlib1g-dev libzip-dev libxml2-dev libpng-dev libssh-dev
RUN rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-install curl bcmath zip iconv xml

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
RUN printf '[PHP]\ndate.timezone="Asia/Tehran"\n' > /usr/local/etc/php/conf.d/tzone.ini

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php composer-setup.php --install-dir=/usr/local/bin --filename=composer
RUN rm -rf composer-setup.php && composer config --global repos.packagist composer https://packagist.org

WORKDIR /usr/share/nginx
