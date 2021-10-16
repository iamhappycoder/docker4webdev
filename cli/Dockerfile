FROM ubuntu:20.04

ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=UTC

#
# Common
#
RUN apt update -y
RUN apt install -y curl unzip software-properties-common

#
# ppa-ondrej-php
#
RUN LC_ALL=C.UTF-8 add-apt-repository -y ppa:ondrej/php
RUN apt update -y

#
# PHP 8.0
#
RUN apt install -y \
    php8.0-cli \
    php8.0-fpm \
    php8.0-xdebug \
    php8.0-xml \
    php8.0-mbstring \
    php-redis \
    php8.0-mysql \
    php8.0-curl

RUN for sapi in cli fpm; \
do \
    if [ -d /etc/php/8.0/$sapi ]; \
    then \
        echo "memory_limit = 1G\nerror_reporting = E_ALL\ndate.timezone = UTC\n" | tee --append /etc/php/8.0/$sapi/php.ini; \
    fi; \
done

RUN echo "\nopcache.enable=1\nopcache.enable_cli=1" | tee --append /etc/php/8.0/mods-available/opcache.ini > /dev/null
RUN echo "\nxdebug.var_display_max_data = -1\nxdebug.var_display_max_children = -1\nxdebug.var_display_max_depth = -1\nxdebug.mode = debug\nxdebug.discover_client_host = true\nxdebug.client_port = 9001" | tee --append /etc/php/8.0/mods-available/xdebug.ini > /dev/null

RUN sed -i \
        -e 's/;daemonize = yes/daemonize = no/g' \
        -e 's/pid = \/run\/php\/php8.0-fpm.pid/pid = \/run\/php8.0-fpm.pid/g' \
        /etc/php/8.0/fpm/php-fpm.conf

RUN sed -i \
        -e 's/listen = \/run\/php\/php8.0-fpm.sock/listen = 9000/g' \
        /etc/php/8.0/fpm/pool.d/www.conf

#
# NodeJS (for Webpack Encore)
#
RUN curl -sL https://deb.nodesource.com/setup_14.x | bash -
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list

RUN apt update -y
RUN apt install -y nodejs yarn

#
# Composer
#
RUN curl -LsS  https://getcomposer.org/composer.phar -o /usr/local/bin/composer
RUN chmod a+x /usr/local/bin/composer

EXPOSE 9000
ENTRYPOINT ["php-fpm8.0", "--force-stderr", "--fpm-config", "/etc/php/8.0/fpm/php-fpm.conf"]