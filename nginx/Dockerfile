FROM debian:stretch

ENV DEBIAN_FRONTEND noninteractive

# Install what we need
RUN apt-get update && apt-get upgrade -y && apt-get install -y \
	git \
	libav-tools \
	nginx \
	php-fpm \
	php-gd \
	php-mysql \
  composer \
	supervisor

# Install Sonerezh
RUN git clone --depth 1 https://github.com/Sonerezh/sonerezh.git /usr/share/nginx/sonerezh && \
	php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
	php -r "if (hash_file('sha384', 'composer-setup.php') === '906a84df04cea2aa72f40b5f787e49f22d4c2f19492ac310e8cba5b96ac8b64115ac402c8cd292b8a03482574915d1a8') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" && \
	php composer-setup.php --install-dir /usr/bin --filename composer && \
	php -r "unlink('composer-setup.php');" && \
	composer install -d /usr/share/nginx/sonerezh && \
	chown -R www-data: /usr/share/nginx/sonerezh && \
	chmod 775 -R /usr/share/nginx/sonerezh

# Prepare volume
RUN mkdir /music && \
    	mkdir /usr/share/nginx/sonerezh/app/webroot/img/thumbnails && \
	ln -s /usr/share/nginx/sonerezh/app/webroot/img/thumbnails /thumbnails && \
	chown www-data: /music

VOLUME /music
VOLUME /thumbnails

# Copy image configuration
RUN rm -f /etc/nginx/sites-enabled/default
COPY database.php /usr/share/nginx/sonerezh/app/Config/database.php
COPY default /etc/nginx/sites-enabled/default
COPY supervisord.conf etc/supervisor/conf.d/supervisord.conf
COPY docker-entrypoint.sh /entrypoint.sh
RUN mkdir /run/php

EXPOSE 80

ENTRYPOINT ["/entrypoint.sh"]
CMD ["/usr/bin/supervisord"]
