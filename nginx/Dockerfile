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
	php -r "if (hash_file('sha384', 'composer-setup.php') === '756890a4488ce9024fc62c56153228907f1545c228516cbf63f885e036d37e9a59d27d63f46af1d4d07ee0f76181c7d3') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" && \
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
COPY docker-entrypoint.sh /entrypoint.sh && \
RUN mkdir /run/php

EXPOSE 80

ENTRYPOINT ["/entrypoint.sh"]
CMD ["/usr/bin/supervisord"]
