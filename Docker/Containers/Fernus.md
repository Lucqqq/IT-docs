Fernus is a web-service which lets you create multiple dashboards to view all your services. It also has the ability to connect to the API of certain other services e.g. Nextcloud, Plex.
Fernus is running within a [[Docker]]-Container and setup with the following [[Docker-Compose]] file:
```yml
version: "3"

services:
  fenrus:
    image: revenz/fenrus
    container_name: fenrus
    environment:
      - TZ=Europe/Berlin
    volumes:
      - /path/to/data:/app/data
      - /path/to/images:/app/wwwroot/images
#ports:
    #  - 9500:3000
    restart: unless-stopped
    networks:
      - proxy
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.fenrus.entrypoints=http"
      - "traefik.http.routers.fenrus.rule=Host(`fenrus.domain.com`)"
      - "traefik.http.middlewares.fenrus-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.fenrus.middlewares=fenrus-https-redirect"
      - "traefik.http.routers.fenrus-secure.entrypoints=https"
      - "traefik.http.routers.fenrus-secure.rule=Host(`fenrus.domain.com`)"
      - "traefik.http.routers.fenrus-secure.tls=true"
      - "traefik.http.routers.fenrus-secure.service=fenrus"
      - "traefik.http.services.fenrus.loadbalancer.server.port=3000"
      - "traefik.docker.network=proxy"
      - 'traefik.http.routers.fenrus-secure.middlewares=authelia@docker'

networks:
  proxy:
    external: true
```