################################################################################
# Base image
################################################################################

FROM nginx

################################################################################
# Build instructions
################################################################################

# Remove default nginx configs.
RUN rm -f /etc/nginx/conf.d/*

# Install packages
RUN apt-get update && apt-get install -y \
  supervisor \
  curl \
  wget \
  php5-curl \
  php5-fpm \
  php5-gd \
  php5-memcached \
  php5-mysql \
  php5-mcrypt \
  php5-sqlite \
  php5-xdebug \
  php-apc

# Ensure that PHP5 FPM is run as root.
RUN sed -i "s/user = www-data/user = root/" /etc/php5/fpm/pool.d/www.conf
RUN sed -i "s/group = www-data/group = root/" /etc/php5/fpm/pool.d/www.conf

# Pass all docker environment
RUN sed -i '/^;clear_env = no/s/^;//' /etc/php5/fpm/pool.d/www.conf

# Get access to FPM-ping page /ping
RUN sed -i '/^;ping\.path/s/^;//' /etc/php5/fpm/pool.d/www.conf
# Get access to FPM_Status page /status
RUN sed -i '/^;pm\.status_path/s/^;//' /etc/php5/fpm/pool.d/www.conf

# Prevent PHP Warning: 'xdebug' already loaded.
# XDebug loaded with the core
RUN sed -i '/.*xdebug.so$/s/^/;/' /etc/php5/mods-available/xdebug.ini

# install PHPUnit
RUN apt-get update -y
RUN apt-get install -y git

################################################################################
# Application files
################################################################################
COPY www /var/www

RUN cd /var/www/wp-content/plugins && git clone https://github.com/woothemes/woocommerce.git
RUN cd /var/www/wp-content/plugins && git clone https://github.com/claudiosmweb/woocommerce-extra-checkout-fields-for-brazil.git


# Add configuration files
COPY docker/development/wordpress/files/nginx.conf /etc/nginx/
COPY docker/development/wordpress/files/default.vhost /etc/nginx/conf.d/
COPY docker/development/wordpress/files/supervisord.conf /etc/supervisor/conf.d/
COPY docker/development/wordpress/files/php.ini /etc/php5/fpm/conf.d/40-custom.ini

################################################################################
# Ports
################################################################################

EXPOSE 80 443 9000

################################################################################
# Entrypoint
################################################################################

ENTRYPOINT ["/usr/bin/supervisord"]
