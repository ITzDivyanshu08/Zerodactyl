FROM alpine AS downloader
ARG PANEL_VERSION=v1.11.5
RUN apk add --no-cache curl tar && \
    mkdir -p /pterodactyl && \
    curl -Lo panel.tar.gz https://github.com/pterodactyl/panel/releases/download/${PANEL_VERSION}/panel.tar.gz && \
    tar -xzvf panel.tar.gz -C /pterodactyl

FROM php:8.2-fpm-alpine AS php_base
RUN apk add --no-cache \
        freetype-dev \
        libjpeg-turbo-dev \
        libpng-dev \
        unzip \
        libzip-dev \
        git && \
    docker-php-ext-configure gd --with-freetype --with-jpeg && \
    docker-php-ext-install -j$(nproc) gd bcmath pdo_mysql zip
RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"
COPY php-extra.ini $PHP_INI_DIR/conf.d/custom.ini
COPY php-fpm-extra.conf /usr/local/etc/php-fpm.d/custom-fpm.conf

FROM php_base AS dependencies
WORKDIR /var/www/html
COPY --from=composer /usr/bin/composer /usr/bin/composer
COPY --from=downloader /pterodactyl/ .
RUN composer install --no-dev --optimize-autoloader
ADD https://raw.githubusercontent.com/eficode/wait-for/master/wait-for /usr/local/bin/wait-for
RUN chmod +x /usr/local/bin/wait-for

FROM php_base AS final
WORKDIR /var/www/html
COPY --from=dependencies --chown=www-data:www-data /var/www/html/ /var/www/panel/
COPY --from=dependencies --chown=www-data:www-data /var/www/html/.env.example /var/www/panel/.env
COPY --from=dependencies /usr/local/bin/wait-for /usr/local/bin/wait-for
COPY cronfile /etc/cron.d/panel-cron
RUN crontab /etc/cron.d/panel-cron && \
    chmod -R 755 /var/www/panel/storage /var/www/panel/bootstrap/cache

EXPOSE 80
ENTRYPOINT ["/bin/sh", "-c", "/usr/local/bin/wait-for database:3306 -t 30 && cp -fp /var/www/html/.env /var/www/panel/ || : && cp -rfpT /var/www/panel /var/www/html || : && yes no | php artisan key:generate && php artisan migrate --force && php artisan db:seed --force && crond && chown -R www-data:www-data * || : && php-fpm"]