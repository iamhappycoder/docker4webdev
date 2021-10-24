FROM ubuntu:18.04

#
# Common
#
RUN apt update -y

#
# Apache 2.4
#
RUN apt install -y apache2
RUN a2enmod proxy proxy_fcgi rewrite expires headers ssl
RUN a2ensite default-ssl

RUN sed -i 's/AllowOverride None/AllowOverride All/g' /etc/apache2/apache2.conf
COPY 000-default.conf /etc/apache2/sites-available/000-default.conf

#
# NodeJS
#
RUN apt install -y curl
RUN curl -sL https://deb.nodesource.com/setup_14.x | bash -
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list

RUN apt update -y
RUN apt install -y nodejs yarn

EXPOSE 80
ENTRYPOINT ["apache2ctl", "-D", "FOREGROUND"]