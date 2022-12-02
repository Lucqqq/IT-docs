# Prometheus

Prometheus is a database working with [[Grafana]] and [[Node-Exporter]]. It is scraping the metrics from [[Node-Exporter]] (see "prometheus.yml") and then serves it for [[Grafana]] to display it. Prometheus is running within a [[Docker]]-Container.

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

The "prometheus.yml" file mounted in "/etc/prometheus" defines the scraping-jobs and looks like this:
```yml
global:
  scrape_interval:     15s # By default, scrape targets every 15 seconds.

scrape_configs:
  - job_name: 'prometheus'
    # Override the global default and scrape targets from this job every 5 seconds.
    scrape_interval: 5s
    static_configs:
      - targets: ['127.0.0.1:9090']

  - job_name: 'node_exporter'
    static_configs:
      - targets: ['node_exporter:9100']
```