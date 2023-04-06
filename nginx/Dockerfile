FROM debian:bullseye
ENV DEBIAN_FRONTEND noninteractive

# Install what we need
RUN apt-get update && apt-get upgrade -y && apt-get install -y \
	ffmpeg \
	git \
	nginx \
	php-fpm \
	php-gd \
	php-mysql \
  	composer \
	supervisor

# Install Sonerezh
COPY --from=composer/composer /usr/bin/composer /usr/bin/composer
RUN git clone --depth 1 https://github.com/mrporcles/sonerezh.git /usr/share/nginx/sonerezh && \
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
