# DashDot

DashDot is a simple dashboard showing the gerneral metrics of your machine.
These metrics include information about:
- CPU
- Memory
- Storage
- Networkspeed 

DashDot is running within a [[Docker]]-Container and setup with the following [[Docker-Compose]] file:
```yml
version: '3.5'

services:
  dash:
    image: mauricenino/dashdot:latest
    restart: unless-stopped
    privileged: true
    #ports:
     # - '3001:3001'
    volumes:
      - /:/mnt/host:ro
    environment:
      DASHDOT_ENABLE_CPU_TEMPS: "true"
      DASHDOT_ENABLE_STORAGE_SPLIT_VIEW: "true"
      DASHDOT_FS_DEVICE_FILTER: sda,sdc,sdd,sde
      DASHDOT_FS_VIRTUAL_MOUNTS: /dev/md127p1
      DASHDOT_WIDGET_LIST: os,cpu,storage,ram,network
      DASHDOT_STORAGE_LABEL_LIST: brand,size,type
      DASHDOT_OVERRIDE_STORAGE_BRANDS: Seagate
      DASHDOT_OVERRIDE_STORAGE_SIZES: 3844418552000
      DASHDOT_OVERRIDE_STORAGE_TYPES: Raid1
      DASHDOT_RAM_LABEL_LIST: size,type,frequency
      DASHDOT_OVERRIDE_RAM_TYPE: DDR3
      DASHDOT_OVERRIDE_NETWORK_INTERFACE_SPEED: 1000
    networks:
      - proxy
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.dash.entrypoints=http"
      - "traefik.http.routers.dash.rule=Host(`dash.domain.com`)"
      - "traefik.http.middlewares.dash-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.dash.middlewares=dash-https-redirect"
      - "traefik.http.routers.dash-secure.entrypoints=https"
      - "traefik.http.routers.dash-secure.rule=Host(`dash.domain.com`)"
      - "traefik.http.routers.dash-secure.tls=true"
      - "traefik.http.routers.dash-secure.service=dash"
      - "traefik.http.services.dash.loadbalancer.server.port=3001"
      - "traefik.docker.network=proxy"
      - 'traefik.http.routers.dash-secure.middlewares=authelia@docker'

networks:
  proxy:
    external: true
```
