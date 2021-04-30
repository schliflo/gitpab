ARG container
ARG tag
FROM webdevops/${container}:${tag}

COPY app.sh /
RUN /bin/bash /app.sh

COPY --from=composer:1 /usr/bin/composer /usr/local/bin/composer

WORKDIR /var/www/html/
