# Web-Terminal

This is a [[Docker]]-Container enabeling web-access to a terminal-session. Note that this is a terminal-session within the container and not on the host os.

[[Docker-Compose]]file:
```yml
version: "3"

services:
  webconsole:
    image: raonigabriel/web-terminal:latest
    restart: unless-stopped
    volumes:
      - /path/to/data:/data
    networks:
      - proxy
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.webconsole.entrypoints=http"
      - "traefik.http.routers.webconsole.rule=Host(`webconsole.domain.com`)"
      - "traefik.http.middlewares.webconsole-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.webconsole.middlewares=webconsole-https-redirect"
      - "traefik.http.routers.webconsole-secure.entrypoints=https"
      - "traefik.http.routers.webconsole-secure.rule=Host(`webconsole.domain.com`)"
      - "traefik.http.routers.webconsole-secure.tls=true"
      - "traefik.http.routers.webconsole-secure.service=webconsole"
      - "traefik.http.services.webconsole.loadbalancer.server.port=7681"
      - "traefik.docker.network=proxy"
      - 'traefik.http.routers.webconsole-secure.middlewares=authelia@docker'

networks:
  proxy:
    external: true
```