FROM php:8.3-fpm-alpine
 
  
RUN apk update --no-cache && \
    apk upgrade --no-cache

RUN apk add --no-cache \
    $PHPIZE_DEPS 
 
# 安装相关的依耐包
RUN apk --update add wget \
  curl \
  curl-dev \
  git \
  build-base \
  libmemcached-dev \
  libmcrypt-dev \
  libxml2-dev \
  zlib-dev \
  oniguruma-dev \
  g++ \
  libtool \
  make \
  icu-dev \
  linux-headers \ 
  $PHPIZE_DEPS 
  
 
# 安装 mysqli mbstring pdo pdo_mysql xml pcntl
RUN docker-php-ext-install pdo pdo_mysql mysqli mbstring intl bcmath opcache
 
# 安装GD库 7.4 安装参数发生变化 @https://www.php.net/manual/zh/migration74.other-changes.php#migration74.other-changes.pkg-config
RUN apk add --update --no-cache freetype-dev libjpeg-turbo-dev jpeg-dev libpng-dev; \
        docker-php-ext-configure gd --with-freetype=/usr/lib/ --with-jpeg=/usr/lib/ && \
        docker-php-ext-install gd; 
 
RUN apk add --update --no-cache judy-dev bsd-compat-headers && \
    git clone https://github.com/arnaud-lb/php-memory-profiler.git /tmp/php-profiler-extension && \
    ( \
        cd /tmp/php-profiler-extension && \
        phpize && \
        ./configure && \
        make -j$(nproc) && \
        make install \
    )
    
# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
RUN { \
		echo 'opcache.memory_consumption=128'; \
		echo 'opcache.interned_strings_buffer=8'; \
		echo 'opcache.max_accelerated_files=20000'; \
		echo 'opcache.revalidate_freq=60'; \
		echo 'opcache.fast_shutdown=1'; \
	} > $PHP_INI_DIR/conf.d/opcache-recommended.ini
RUN { \
		echo 'memory_limit=256M'; \
		echo 'upload_max_filesize=10M'; \
		echo 'expose_php=off'; \
	} > $PHP_INI_DIR/conf.d/zzz.ini
 
# Composer install
RUN curl -s http://getcomposer.org/installer | php && \ 
    mv composer.phar /usr/local/bin/composer


# Install Drush
#RUN composer require  drush/drush
# # Drush install
RUN php -r "readfile('https://github.com/drush-ops/drush/releases/download/8.4.11/drush.phar');" > /usr/local/bin/drush && \
    chmod +x /usr/local/bin/drush

# Set working directory
WORKDIR /var/www/

# Create a new Drupal project
RUN composer create-project drupal/recommended-project drupal



RUN chown -R www-data:www-data /var/www/
# RUN ln -sf /opt/drupal/web /var/www/html; \

USER www-data
WORKDIR /var/www
VOLUME /var/www/drupal
CMD ["php-fpm"]
