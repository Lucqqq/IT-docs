```yml
version: "2.1"
services:
  code-server:
    image: lscr.io/linuxserver/code-server:latest
    container_name: code-server
    environment:
      - PASSWORD=
      - SUDO_PASSWORD=
      - DEFAULT_WORKSPACE=/config/workspace
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
      - "traefik.http.routers.code-server.entrypoints=http"
      - "traefik.http.routers.code-server.rule=Host(`code.domain.com`)"
      - "traefik.http.middlewares.code-server-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.code-server.middlewares=code-server-https-redirect"
      - "traefik.http.routers.code-server-secure.entrypoints=https"
      - "traefik.http.routers.code-server-secure.rule=Host(`code.domain.com`)"
      - "traefik.http.routers.code-server-secure.tls=true"
      - "traefik.http.routers.code-server-secure.service=code-server"
      - "traefik.http.services.code-server.loadbalancer.server.port=8443"
      - "traefik.docker.network=proxy"
      - 'traefik.http.routers.code-server-secure.middlewares=authelia@docker'

networks:
  proxy:
    external: true
```