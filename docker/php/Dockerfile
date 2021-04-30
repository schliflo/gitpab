FROM node:8 as builder

RUN mkdir /frontend
WORKDIR /frontend

COPY ./package.json .
COPY ./package-lock.json .

RUN npm ci

COPY ./webpack.mix.js ./webpack.mix.js
COPY ./resources/assets ./resources/assets

RUN npm run prod

# ---------------------

FROM webdevops/php:7.2

WORKDIR /var/www/html

# Install packages
RUN apt update -y \
    && apt install -y ssh git zip bzip2 wget libmcrypt-dev cron mc

# composer install: https://hub.docker.com/_/composer?tab=description#php-extensions
COPY --from=composer:1 /usr/bin/composer /usr/bin/composer

RUN touch /var/log/cron.log

ADD ./docker/php/entrypoint.sh /root/entrypoint.sh
ADD ./docker/wait-for-it.sh /root/wait-for-it.sh
RUN chmod 755 /root/entrypoint.sh \
    && chmod 755 /root/wait-for-it.sh

COPY ./docker/cron/crontab /etc/cron.d/app
RUN crontab /etc/cron.d/app

COPY ./composer.json .
COPY ./composer.lock .
RUN composer install --no-dev --no-autoloader

# npm run build
COPY --from=builder /frontend/public/css ./public/css
COPY --from=builder /frontend/public/js ./public/js

# Project files
ADD . .
COPY ./docker/php/.env.template ./.env
RUN composer dump-autoload
RUN chown -R $APPLICATION_USER:$APPLICATION_GROUP .
RUN chmod -R 777 .

ENTRYPOINT ["/root/entrypoint.sh"]
