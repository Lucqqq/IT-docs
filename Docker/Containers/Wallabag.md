```yml
version: '3'
services:
  wallabag:
    image: wallabag/wallabag
    environment:
      - MYSQL_ROOT_PASSWORD=wallaroot
      - SYMFONY__ENV__DATABASE_DRIVER=pdo_mysql
      - SYMFONY__ENV__DATABASE_HOST=dbw
      - SYMFONY__ENV__DATABASE_PORT=3306
      - SYMFONY__ENV__DATABASE_NAME=wallabag
      - SYMFONY__ENV__DATABASE_USER=wallabag
      - SYMFONY__ENV__DATABASE_PASSWORD=wallapass
      - SYMFONY__ENV__DATABASE_CHARSET=utf8mb4
      - SYMFONY__ENV__MAILER_HOST=127.0.0.1
      - SYMFONY__ENV__MAILER_USER=~
      - SYMFONY__ENV__MAILER_PASSWORD=~
      - SYMFONY__ENV__FROM_EMAIL=wallabag@example.com
      - SYMFONY__ENV__DOMAIN_NAME=https://wallabag.domain.com
      - SYMFONY__ENV__SERVER_NAME="Your wallabag instance"
    ports:
      - "80"
    volumes:
      - /path/to/images:/var/www/wallabag/web/assets/images
    healthcheck:
      test: ["CMD", "wget" ,"--no-verbose", "--tries=1", "--spider", "http://localhost"]
      interval: 1m
      timeout: 3s
    depends_on:
      - dbw
      - redis
    networks:
      - proxy
      - wallabag
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.wallabag.entrypoints=http"
      - "traefik.http.routers.wallabag.rule=Host(`wallabag.domain.com`)"
      - "traefik.http.middlewares.wallabag-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.wallabag.middlewares=wallabag-https-redirect"
      - "traefik.http.routers.wallabag-secure.entrypoints=https"
      - "traefik.http.routers.wallabag-secure.rule=Host(`wallabag.domain.com`)"
      - "traefik.http.routers.wallabag-secure.tls=true"
      - "traefik.http.routers.wallabag-secure.service=wallabag"
      - "traefik.http.services.wallabag.loadbalancer.server.port=80"
      - "traefik.docker.network=proxy"
#      - 'traefik.http.routers.wallabag-secure.middlewares=authelia@docker'
      
  dbw:
    image: mariadb
    environment:
      - MYSQL_ROOT_PASSWORD=wallaroot
    volumes:
      - /path/to/data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin" ,"ping", "-h", "localhost"]
      interval: 20s
      timeout: 3s
    networks:
      - wallabag
  redis:
    image: redis:alpine
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 20s
      timeout: 3s
    networks:
      - wallabag

networks:
  proxy:
    external: true
  wallabag:
```