# Node-Exporter

Node-Exporter is a [[Docker]]-Container which reads all systemmetrics and serves them to [[Prometheus]].

The complete [[Docker-Compose]] with [[Grafana]], [[Prometheus]] and [[Node-Exporter]] looks like this: 
```yml
version: '3'

volumes:
  prometheus-data:
    driver: local
  grafana-data:
    driver: local

services:
  node_exporter:
    image: quay.io/prometheus/node-exporter:latest
    container_name: node_exporter
    command:
      - '--path.rootfs=/host'
    pid: host
    restart: unless-stopped
    networks:
      - proxy
    volumes:
      - '/:/host:ro,rslave'
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    volumes:
      - /etc/prometheus:/etc/prometheus
      - prometheus-data:/prometheus
    restart: unless-stopped
    networks:
      - proxy
    command:
      - "--config.file=/etc/prometheus/prometheus.yml"
  grafana:
    image: grafana/grafana-oss:latest
    container_name: grafana
    volumes:
      - grafana-data:/var/lib/grafana
    restart: unless-stopped
    networks:
      - proxy
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.grafana.entrypoints=http"
      - "traefik.http.routers.grafana.rule=Host(`grafana.domain.com`)"
      - "traefik.http.middlewares.grafana-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.grafana.middlewares=grafana-https-redirect"
      - "traefik.http.routers.grafana-secure.entrypoints=https"
      - "traefik.http.routers.grafana-secure.rule=Host(`grafana.domain.com`)"
      - "traefik.http.routers.grafana-secure.tls=true"
      - "traefik.http.routers.grafana-secure.service=grafana"
      - "traefik.http.services.grafana.loadbalancer.server.port=3000"
      - "traefik.docker.network=proxy"
      - 'traefik.http.routers.grafana-secure.middlewares=authelia@docker'

networks:
  proxy:
    external: true
```