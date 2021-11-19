# Dockerizing_NGINX_PHP-FPM

```sh
# docker ps
CONTAINER ID   IMAGE                COMMAND                  CREATED          STATUS          PORTS                                   NAMES
0926980cb9a6   php:7.4-fpm-alpine   "docker-php-entrypoi…"   11 seconds ago   Up 10 seconds   9000/tcp                                php-fpm
7f89892732a6   nginx:1.20-alpine    "/docker-entrypoint.…"   11 seconds ago   Up 10 seconds   0.0.0.0:8081->80/tcp, :::8081->80/tcp   nginx
```

```sh
/root/dock/
├── conf
│   └── website.conf
├── docker-compose.yml
└── site
    └── index.php
```
