# Filebrowser

Filebrowser is a web-service which allowes you to manage files with an explorer like interface. It also allows for easy downloading of the files.
Filebrowser is running within a [[Docker]]-Container and setup with the following [[Docker-Compose]] file:
```yml
version: "3"

services:
  filebrowser:
    image: hurlenko/filebrowser
    user: "${UID}:${GID}"
    volumes:
      - /path/to/files:/data
      - /path/to/config:/config
    restart: always
    environment:
      - FB_BASEURL=/filebrowser
    networks:
      - proxy
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.filebrowser.entrypoints=http"
      - "traefik.http.routers.filebrowser.rule=Host(`filebrowser.domain.com`)"
      - "traefik.http.middlewares.filebrowser-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.filebrowser.middlewares=filebrowser-https-redirect"
      - "traefik.http.routers.filebrowser-secure.entrypoints=https"
      - "traefik.http.routers.filebrowser-secure.rule=Host(`filebrowser.domain.com`)"
      - "traefik.http.routers.filebrowser-secure.tls=true"
      - "traefik.http.routers.filebrowser-secure.service=filebrowser"
      - "traefik.http.services.filebrowser.loadbalancer.server.port=8080"
      - "traefik.docker.network=proxy"
      - 'traefik.http.routers.filebrowser-secure.middlewares=authelia@docker'

networks:
  proxy:
    external: true
```