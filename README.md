# docker-phpdev

Project template for PHP development in a Docker environment.

Usage:

- Create .env from .env.dist

- Put project files in app/

- Build

  `````$ docker-compose build`````

- Start containers

  `````$ docker-compose up`````
  
- Run commands within the cli container

  ```
  $ docker-compose exec cli composer install -vvv
  $ docker-compose exec cli vendor/bin/simple-phpunit
  ```
  
NOTES:

- Add PHP modules and/or other CLI tools in cli/Dockerfile
- The containers can be reached from other services via their container name.
- Using with Symfony

  Change web/000-default.conf

    ```fcgi://cli:9000/app/$1```
    
    to
    
    ```fcgi://cli:9000/app/public/$1```
    
  Change web service volumes in docker-compose.yml
   
   ```- ./app:/var/www/html```
   
   to
   
   ```- ./app/public:/var/www/html```
   
  Run something like the following if creating a project from scratch
  
  ```
  $ docker-compose exec cli composer create-project symfony/skeleton .
  $ docker-compose exec cli composer require --dev phpunit/phpunit symfony/test-pack
  $ docker-compose exec cli vendor/bin/simple-phpunit
  ```
- The container names need to be unique when using this template for multiple projects

  Rename the containers

    from
    ```
    container_name: cli
    container_name: web
    container_name: db
    ```
    
    to
    
    ```
    container_name: cli-<project name>
    container_name: web-<project name>
    container_name: db-<project name>
    ```
  
  Change host port numbers but keep container port numbers
  
    ```
    - "8001:80"
    - "4431:443"
    - "33061:3306"
    ```
- Switching to PostgeSQL

  Change cli/Dockerfile
  
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
      ports:
        - "54321:5432"
  ```
  
  Remove db/data/.gitignore because PostgreSQL does not like it being present
  
  ```rm -rf db/data/.gitignore```


- Using with an existing project.

  .env
  ```
  APP_DIR=[path to project dir]
  ```