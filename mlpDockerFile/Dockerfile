FROM alexcheng/apache2-php7:7.1.11

LABEL maintainer="alexcheng1982@gmail.com"
LABEL version="2.2.7"
LABEL description="Magento 2.2.7"

ENV MAGENTO_VERSION 2.2.7
ENV INSTALL_DIR /var/www/html
ENV COMPOSER_HOME /var/www/.composer/

RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer
COPY ./auth.json $COMPOSER_HOME

RUN requirements="libpng12-dev libmcrypt-dev libmcrypt4 libcurl3-dev libfreetype6 libjpeg-turbo8 libjpeg-turbo8-dev libpng12-dev libfreetype6-dev libicu-dev libxslt1-dev" \
    && apt-get update \
    && apt-get install -y $requirements \
    && rm -rf /var/lib/apt/lists/* \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd \
    && docker-php-ext-install mcrypt \
    && docker-php-ext-install mbstring \
    && docker-php-ext-install zip \
    && docker-php-ext-install intl \
    && docker-php-ext-install xsl \
    && docker-php-ext-install soap \
    && docker-php-ext-install bcmath \
    && requirementsToRemove="libpng12-dev libmcrypt-dev libcurl3-dev libpng12-dev libfreetype6-dev libjpeg-turbo8-dev" \
    && apt-get purge --auto-remove -y $requirementsToRemove

RUN chsh -s /bin/bash www-data

RUN chown -R www-data:www-data /var/www
RUN su www-data -c "composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition $INSTALL_DIR $MAGENTO_VERSION"

RUN cd $INSTALL_DIR \
    && find . -type d -exec chmod 770 {} \; \
    && find . -type f -exec chmod 660 {} \; \
    && chmod u+x bin/magento

COPY ./install-magento /usr/local/bin/install-magento
RUN chmod +x /usr/local/bin/install-magento

COPY ./install-sampledata /usr/local/bin/install-sampledata
RUN chmod +x /usr/local/bin/install-sampledata

RUN a2enmod rewrite
RUN echo "memory_limit=2048M" > /usr/local/etc/php/conf.d/memory-limit.ini

RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Add cron job
ADD crontab /etc/cron.d/magento2-cron
RUN chmod 0644 /etc/cron.d/magento2-cron \
    && crontab -u www-data /etc/cron.d/magento2-cron

COPY ./disable-module /usr/local/bin/disable-module
RUN chmod +x /usr/local/bin/disable-module

RUN apt-get update && apt-get install -y ftp

COPY ./sorefoz.sh /usr/local/bin/sorefoz.sh
RUN chmod +x /usr/local/bin/sorefoz.sh

COPY ./sorefoz_cron /etc/cron.d/sorefoz_cron
RUN chmod 0644 /etc/cron.d/sorefoz_cron

COPY ./sorefoz_conv.sh /usr/local/bin/sorefoz_conv.sh
RUN chmod +x /usr/local/bin/sorefoz_conv.sh

RUN touch /var/log/sorefoz_cron.log

RUN composer global require phpoffice/phpspreadsheet

RUN apt-get install -y wget

RUN apt-get install -y php-dev autoconf automake

RUN cd /tmp \
    && wget http://xdebug.org/files/xdebug-2.7.2.tgz \
    && tar -zxvf xdebug-2.7.2.tgz \
    && cd  xdebug-2.7.2 \
    && /usr/local/bin/phpize \
    && ./configure --enable-xdebug --with-php-config=/usr/local/bin/php-config \
    && make \
    && cp modules/xdebug.so /usr/local/lib/php/extensions/no-debug-non-zts-20160303/

# add xdebug configurations
RUN { \
        echo '[xdebug]'; \
        echo 'zend_extension=/usr/local/lib/php/extensions/no-debug-non-zts-20160303/xdebug.so'; \
        echo 'xdebug.remote_enable=on'; \
        echo 'xdebug.remote_autostart=on'; \
        echo 'xdebug.remote_connect_back=off'; \
        echo 'xdebug.remote_handler=dbgp'; \
        echo 'xdebug.remote_log=/tmp/xdebug_remote.log'; \
        echo 'xdebug.profiler_enable=off'; \
        echo 'xdebug.profiler_output_dir="/var/www/html"'; \
        echo 'xdebug.remote_port=9001'; \
        echo 'xdebug.remote_host=172.254.254.254'; \
    } > /usr/local/etc/php/conf.d/xdebug.ini

RUN apt-get update
RUN apt-get install -y net-tools iputils-ping

WORKDIR $INSTALL_DIR

