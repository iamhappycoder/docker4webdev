# docker-phpdev

Project template for PHP development in a Docker environment.

Usage:

- Create .env from .env.dist

- Put project files in app/

- Build

  `````$ docker-compose build`````

- Start containers

  `````$ docker-compose up`````
  
- Run commands within the php container

  ```
  $ docker-compose exec php composer install -vvv
  $ docker-compose exec php vendor/bin/simple-phpunit
  ```

- Run commands within the nodejs container

  ```
  $ docker-compose run nodejs yarn install
  $ docker-compose run nodejs yarn start
  ```
  
- Create host file for web server

  ```
  $ docker-compose ps -q web | cut -c -12
  [CONTAINER-ID]
  
  $ docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [CONTAINER-ID]
  [CONTAINER-IP]
  ```

  /etc/hosts
  ```
  [CONTAINER-IP]    docker-phpdev.local
  ```
  
NOTES:

- Add PHP modules and/or other CLI tools in php/Dockerfile
- The containers can be reached from other services via their container name.
- Using with Symfony

  Change web/000-default.conf

    ```fcgi://php:9000/app/$1```
    
    to
    
    ```fcgi://php:9000/app/public/$1```
    
  Change web service volumes in docker-compose.yml
   
   ```- ./app:/var/www/html```
   
   to
   
   ```- ./app/public:/var/www/html```
   
  Run something like the following if creating a project from scratch
  
  ```
  $ docker-compose exec php composer create-project symfony/skeleton .
  $ docker-compose exec php composer require --dev phpunit/phpunit symfony/test-pack
  $ docker-compose exec php vendor/bin/simple-phpunit
  ```
- When using multiple copies of this template, make sure the directory names are unique to each other (https://docs.docker.com/compose/#multiple-isolated-environments-on-a-single-host).

  Unique:

  ```
  ~/backend/docker-phpdev-symfony
  ~/frontend/docker-phpdev-laravel
  ```

  Not unique:

  ```
  ~/backend/docker-phpdev
  ~/frontend/docker-phpdev
  ```

  An alternative is to specify container_name for each service.

- Switching to PostgeSQL

  Change php/Dockerfile
  
  ```php7.4-mysql```
  
  to
  
  ```php7.4-pgsql```
  
  Change db service to
  
  ```
    db:
      container_name: db
      image: postgres:13.0
      volumes:
        - ./db/data:/var/lib/postgresql/data
      environment:
        POSTGRES_PASSWORD: root
  ```
  
  Remove db/data/.gitignore because PostgreSQL does not like it being present
  
  ```rm -rf db/data/.gitignore```


- Using with an existing project.

  .env
  ```
  APP_DIR=[path to project dir]
  ```