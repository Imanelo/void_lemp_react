# Dockerfile for Drupal service
FROM drupal:9.2-php8.0-fpm-alpine

RUN apk update --no-cache && \
    apk upgrade --no-cache

RUN apk add --no-cache \
    $PHPIZE_DEPS 

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
  
# Install additional PHP extensions if needed
# RUN docker-php-ext-install <extension-name>
RUN docker-php-ext-install mbstring intl bcmath
 
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

   
# Install memcached
RUN pecl install memcached  && \
    docker-php-ext-enable memcached 
# # Drush install
RUN php -r "readfile('https://github.com/drush-ops/drush/releases/download/8.4.11/drush.phar');" > /usr/local/bin/drush && \
    chmod +x /usr/local/bin/drush

# # Composer install
RUN curl -s http://getcomposer.org/installer | php && \ 
     mv composer.phar /usr/local/bin/composer 
 
# Copy custom PHP configuration files if needed
# COPY custom.ini /usr/local/etc/php/conf.d/
ENV PHP_V="php80"
COPY config/php.ini /etc/${PHP_V}/conf.d/custom.ini


# Ensure sites/default/files ownership

RUN chown -R www-data:www-data /var/www/html; 
RUN chmod -R 755 /var/www/html;

# Set the working directory


# Expose port if needed
# EXPOSE 9000

# Set any additional environment variables if needed
# ENV EXAMPLE_VARIABLE=value

# Set entrypoint or default command if needed
# ENTRYPOINT ["php"]
# CMD ["-v"]
