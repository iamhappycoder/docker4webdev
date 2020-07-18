# docker-symfonydev

Project template for Symfony development in a Docker environment

Usage:

- Put project files in app/

- Build

  $ docker-compose build

- Start containers

  $ docker-compose up
  
- Run commands within the php container

  $ docker-compose exec php composer install -vvv
  
  $ docker-compose exec php vendor/bin/simple-phpunit