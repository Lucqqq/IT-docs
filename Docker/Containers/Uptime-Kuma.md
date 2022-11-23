```yml
version: "3.1"

services:
  uptime-kuma:
    image: louislam/uptime-kuma:1
    container_name: uptime_kuma
    volumes:
      - /path/to/data:/app/data
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.uptime_kuma.entrypoints=http"
      - "traefik.http.routers.uptime_kuma.rule=Host(`uptime.domain.com`)"
      - "traefik.http.middlewares.uptime_kuma-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.uptime_kuma.middlewares=uptime_kuma-https-redirect"
      - "traefik.http.routers.uptime_kuma-secure.entrypoints=https"
      - "traefik.http.routers.uptime_kuma-secure.rule=Host(`uptime.domain.com`)"
      - "traefik.http.routers.uptime_kuma-secure.tls=true"
      - "traefik.http.routers.uptime_kuma-secure.service=uptime_kuma"
      - "traefik.http.services.uptime_kuma.loadbalancer.server.port=3001"
      - "traefik.docker.network=proxy"
      - 'traefik.http.routers.uptime_kuma-secure.middlewares=authelia@docker'
    networks:
      - proxy
      
networks:
  proxy:
    external: true
```