# Docker compose - Testing a PHP site in NGINX using PHP-FPM
Here, we will be creating two containers for NGINX and PHP-FPM and running a PHP site in the same.

# Docker
Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. 

Docker provides the ability to package and run an application in a loosely isolated environment called a container. The isolation and security allow you to run many containers simultaneously on a given host. Containers are lightweight and contain everything needed to run the application, so you do not need to rely on what is currently installed on the host. You can easily share containers while you work too.
You can learn more about the same in the [website.](https://docs.docker.com/get-started/overview/)

# What Is a Container
A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.
You can learn more about the same in the [website.](https://www.docker.com/resources/what-container)


# Docker compose
Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration.
You can learn more about the same in the [website.](https://docs.docker.com/compose/)


# Installing Docker
> Here I am using AWS Amazon linux server
```sh
amazon-linux-extras install docker -y
systemctl restart docker.service
systemctl enable docker.service
```
Please see the [website](https://docs.docker.com/engine/install/) for more information.

# Installing Docker compose
```sh
curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
```
Please see the [website](https://docs.docker.com/compose/install/) for more information.

# File structure
```sh
/root/dock/
├── conf
│   └── website.conf
├── docker-compose.yml
└── site
    └── index.php
```
# Configuration file
```sh
vim website.conf
```
```sh
server {
  listen 80;
  listen [::]:80;
  access_log off;
  root /var/www/html;
  index index.php;
  server_name example.com;
  server_tokens off;
  location / {
    # First attempt to serve request as file, then
    # as directory, then fall back to displaying a 404.
    try_files $uri $uri/ /index.php?$args;
  }
  # pass the PHP scripts to FastCGI server listening on php-fpm:9000
  location ~ \.php$ {
    fastcgi_split_path_info ^(.+\.php)(/.+)$;
    fastcgi_pass php-fpm:9000;
    fastcgi_index index.php;
    include fastcgi_params;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    fastcgi_param SCRIPT_NAME $fastcgi_script_name;
  }
}
```
# PHP site
> Here, I am using the contents of phpinfo page and adding it in the index.php file which will act as our PHP site.
```sh
vim index.php
```
```sh
<?php
phpinfo();
phpinfo(INFO_MODULES);
?>
```

# Docker Compose
> Note: The compose file should have the name and the extension as docker-compose.yml
```sh
---
version: '3'
services:
  web:
    image: nginx:1.20-alpine
    container_name: nginx
    ports:
      - "8081:80"
    volumes:
      - ./site/:/var/www/html/
      - ./conf/website.conf:/etc/nginx/conf.d/default.conf
    networks:
      - nginxphp

  php-fpm:
    image: php:7.4-fpm-alpine
    container_name: php-fpm
    volumes:
      - ./site/:/var/www/html/
    expose:
      - 9000
    networks:
      - nginxphp

networks:
  nginxphp: 
```
## Execution
- Running a syntax check
```sh
docker-compose config
```
- Executing the yaml file
```sh
docker-compose up -d
```
```sh
# docker ps
CONTAINER ID   IMAGE                COMMAND                  CREATED          STATUS          PORTS                                   NAMES
0926980cb9a6   php:7.4-fpm-alpine   "docker-php-entrypoi…"   11 seconds ago   Up 10 seconds   9000/tcp                                php-fpm
7f89892732a6   nginx:1.20-alpine    "/docker-entrypoint.…"   11 seconds ago   Up 10 seconds   0.0.0.0:8081->80/tcp, :::8081->80/tcp   nginx
```
