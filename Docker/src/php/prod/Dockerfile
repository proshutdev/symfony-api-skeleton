FROM alpine:3.13

LABEL Maintainer="Seyed Hasan Seyedi <hamid.udc@gmail.com>" \
      Description="Lightweight container with Nginx 1.16 & PHP-FPM 7.4 based on Alpine Linux"

# Install packages
RUN apk --no-cache add nginx supervisor curl php7 php7-fpm php7-opcache php7-openssl \
    php7-curl php7-ctype php7-iconv php7-xml php7-common php7-mbstring php7-tokenizer \
    php7-xmlreader php7-xmlwriter php7-json php7-phar php7-zip php7-zlib php7-simplexml

# Install composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php composer-setup.php --install-dir=/usr/local/bin --filename=composer
RUN rm -rf composer-setup.php && composer config --global repos.packagist composer https://packagist.org

# Configure nginx
COPY Docker/config/nginx/default.prod.conf /etc/nginx/nginx.conf

# Remove default server definition
RUN rm /etc/nginx/conf.d/default.conf

# Configure PHP-FPM
COPY Docker/config/php/fpm-pool.conf /etc/php7/php-fpm.d/www.conf
COPY Docker/config/php/php.ini /etc/php7/conf.d/custom.ini

# Configure supervisord
COPY Docker/config/supervisor/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Setup document root
RUN mkdir -p /srv/http

# Make sure files/folders needed by the processes are accessable when they run under the nobody user
RUN chown -R nobody.nobody /srv/http && \
  chown -R nobody.nobody /run && \
  chown -R nobody.nobody /var/lib/nginx && \
  chown -R nobody.nobody /var/log/nginx

# Switch to use a non-root user
USER nobody

# Add application
WORKDIR /srv/http
COPY --chown=nobody api/ /srv/http/
RUN rm -rf ./vendor && composer update && chmod +x -R var/db/

# Expose the port nginx is reachable on
EXPOSE 8080

# Let supervisord start nginx & php-fpm
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]

# Configure a healthcheck to validate that everything is up&running
HEALTHCHECK --timeout=10s CMD curl --silent --fail http://127.0.0.1:8080/healthcheck

