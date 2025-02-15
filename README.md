# Docker4WebDev

Provides a pre-configured setup with commonly used services like PHP, NodeJS, Apache, PostgreSQL, Redis, and Mailhog out-of-the-box.

**WARNING: This project is intended for local development only. It includes services and tools (like Xdebug) that should never be exposed to the internet for security reasons. 
Use this template to quickly set up a local web development environment, but ensure it is not deployed in a production setting.**

Usage:

- Create .env from .env.dist

- Put project files in app/

- Build

  `````$ docker compose build`````

- Start containers

  `````$ docker compose up`````
  
- Run commands within the php container

  ```
  $ docker compose exec php composer install -vvv
  $ docker compose exec php vendor/bin/simple-phpunit
  ```

- Run NodeJS commands within the web container

  ```
  $ docker compose run web yarn install
  $ docker compose run web yarn start
  ```
  
- Create host file for web server

  ```
  $ docker compose ps -q web | cut -c -12
  [CONTAINER-ID]
  
  $ docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [CONTAINER-ID]
  [CONTAINER-IP]
  ```

  /etc/hosts
  ```
  [CONTAINER-IP]    docker4webdev.local
  ```
  
NOTES:

- Add PHP modules and/or other CLI tools in php/Dockerfile
- The containers can be reached from other services via their container name.
- Using with Symfony

  .env

  ```
  PUBLIC_DIR=/public
  ```
  
  Run something like the following if creating a project from scratch
  
  ```
  $ docker compose exec php composer create-project symfony/skeleton .
  $ docker compose exec php composer require --dev symfony/test-pack
  
  $ docker compose exec php setfacl -dR -m u:www-data:rwX -m u:root:rwX var
  $ docker compose exec php setfacl -R -m u:www-data:rwX -m u:root:rwX var
  ```
- When using multiple copies of this template, make sure the directory names are unique to each other (https://docs.docker.com/compose/#multiple-isolated-environments-on-a-single-host).

  Unique:

  ```
  ~/backend/docker4webdev-symfony
  ~/frontend/docker4webdev-laravel
  ```

  Not unique:

  ```
  ~/backend/docker4webdev
  ~/frontend/docker4webdev
  ```

  An alternative is to specify container_name for each service.

- Switching to MariaDB

  Change php/Dockerfile
  
  ```php8.4-pgsql```
  
  to
  
  ```php8.4-mysql```
  
  Change db service to
  
  ```
    db:
    image: mariadb:10.5
    volumes:
      - ./db/data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: master
    ports:
      - "33060:3306"
  ```
  
 - Remove db/data/.gitignore because PostgreSQL does not like it being present
  
  ```rm -rf db/data/.gitignore```


- Using with an existing project.

  .env
  ```
  APP_DIR=[path to project dir]
  ```