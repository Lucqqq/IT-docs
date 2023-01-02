# Heimdall 
Heimdall is a webpage displaying all your services and porviding access to all those webinterfaces of your services.
It is not currently run since it got replaced with [[Homer (Deprecated)]].
It is run via [[Docker]]:
```yml
version: "2.1"
services:
  heimdall:
    image: lscr.io/linuxserver/heimdall:latest
    container_name: heimdall
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true
    networks:
      - proxy
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - /path/to/data:/data
      - /path/to/config:/config
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.heimdall.entrypoints=http"
      - "traefik.http.routers.heimdall.rule=Host(`home.domain.com`)"
      - "traefik.http.middlewares.heimdall-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.heimdall.middlewares=heimdall-https-redirect"
      - "traefik.http.routers.heimdall-secure.entrypoints=https"
      - "traefik.http.routers.heimdall-secure.rule=Host(`home.domain.com`)"
      - "traefik.http.routers.heimdall-secure.tls=true"
      - "traefik.http.routers.heimdall-secure.service=heimdall"
      - "traefik.http.services.heimdall.loadbalancer.server.port=80"
      - "traefik.docker.network=proxy"
      - 'traefik.http.routers.heimdall-secure.middlewares=authelia@docker'

networks:
  proxy:
    external: true
```