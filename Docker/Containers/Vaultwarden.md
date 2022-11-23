```yml
version: '3'

services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    restart: always
    environment:
      WEBSOCKET_ENABLED: "true"  # Enable WebSocket notifications.
      ADMIN_TOKEN: "secret"
      SIGNUPS_ALLOWED: "true"
    volumes:
      - /path/to/vw-data:/data
    networks:
      - proxy
    labels:
      - traefik.enable=true
      - traefik.docker.network=traefik
      - traefik.http.middlewares.redirect-https.redirectScheme.scheme=https
      - traefik.http.middlewares.redirect-https.redirectScheme.permanent=true
      - traefik.http.routers.bitwarden-ui-https.rule=Host(`pass.domain.com`)
      - traefik.http.routers.bitwarden-ui-https.entrypoints=https
      - traefik.http.routers.bitwarden-ui-https.tls=true
      - traefik.http.routers.bitwarden-ui-https.service=bitwarden-ui
      - traefik.http.routers.bitwarden-ui-http.rule=Host(`pass.domain.com`)
      - traefik.http.routers.bitwarden-ui-http.entrypoints=http
      - traefik.http.routers.bitwarden-ui-http.middlewares=redirect-https
      - traefik.http.routers.bitwarden-ui-http.service=bitwarden-ui
      - traefik.http.services.bitwarden-ui.loadbalancer.server.port=80
      - traefik.http.routers.bitwarden-websocket-https.rule=Host(`pass.domain.com`) && Path(`/notifications/hub`)
      - traefik.http.routers.bitwarden-websocket-https.entrypoints=https
      - traefik.http.routers.bitwarden-websocket-https.tls=true
      - traefik.http.routers.bitwarden-websocket-https.service=bitwarden-websocket
      - traefik.http.routers.bitwarden-websocket-http.rule=Host(`pass.domain.com`) && Path(`/notifications/hub`)
      - traefik.http.routers.bitwarden-websocket-http.entrypoints=http
      - traefik.http.routers.bitwarden-websocket-http.middlewares=redirect-https
      - traefik.http.routers.bitwarden-websocket-http.service=bitwarden-websocket
      - traefik.http.services.bitwarden-websocket.loadbalancer.server.port=3012
     # - traefik.http.routers.bitwarden-ui-https.middlewares=authelia@docker
     
networks:
  proxy:
    external: true
```