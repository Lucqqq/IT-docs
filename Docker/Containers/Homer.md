Homer is a webpage displaying all your services and porviding access to all those webinterfaces of your services.
It is currently running on [[Arm Instance]] and proxied through [[Traefik]].
It is run via [[Docker]]:
```yml
version: "2"
services:
  homer:
    image: b4bz/homer
    container_name: homer
    volumes:
      - /path/to/assets:/www/assets
    user: 0:0 # default
    environment:
      - INIT_ASSETS=1 # default
    networks:
      - proxy
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.homer.entrypoints=http"
      - "traefik.http.routers.homer.rule=Host(`home.domain.com`)"
      - "traefik.http.middlewares.homer-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.homer.middlewares=homer-https-redirect"
      - "traefik.http.routers.homer-secure.entrypoints=https"
      - "traefik.http.routers.homer-secure.rule=Host(`home.domain.com`)"
      - "traefik.http.routers.homer-secure.tls=true"
      - "traefik.http.routers.homer-secure.service=homer"
      - "traefik.http.services.homer.loadbalancer.server.port=8080"
      - "traefik.docker.network=proxy"
      - 'traefik.http.routers.homer-secure.middlewares=authelia@docker'

networks:
  proxy:
    external: true
```
At first this page is completly empty.
To configure the page head to the folder mapped to "/www/assets" and edit the config.yml file. This is the current state of the config file:
```yml
---
# Homepage configuration
# See https://fontawesome.com/v5/search for icons options

title: "Homer"
subtitle: "Homer"
logo: "{logo}"

header: false
footer: false 
theme: default
colors:
  light:
    highlight-primary: "#3367d6"
    highlight-secondary: "#4285f4"
    highlight-hover: "#5a95f5"
    background: "#f5f5f5"
    card-background: "#ffffff"
    text: "#363636"
    text-header: "#ffffff"
    text-title: "#303030"
    text-subtitle: "#424242"
    card-shadow: rgba(0, 0, 0, 0.1)
    link: "#3273dc"
    link-hover: "#363636"
  dark:
    highlight-primary: "#a10000"
    highlight-secondary: "#db0000"
    highlight-hover: "#ff5454"
    background: "#131313"
    card-background: "#2b2b2b"
    text: "#eaeaea"
    text-header: "#ffffff"
    text-title: "#fafafa"
    text-subtitle: "#f5f5f5"
    card-shadow: rgba(0, 0, 0, 0.4)
    link: "#3273dc"
    link-hover: "#ffdd57"

services:
  - name: "Category 1"
    icon: "fa-solid fa-database"
    items:
      - name: "Link 1"
        logo: "{logo}"
        subtitle: "subtitle"
        tag: "tag"
        keywords: " "
        url: "https://domain.com/"
        target: "_blank" # optional html a tag target attribute

      - name: "Link 2"
        logo: "{logo}"
        subtitle: "subtitle"
        tag: "tag"
        keywords: " "
        url: "https://domain.com/"
        target: "_blank" # optional html a tag target attribute

  - name: "Category 2"
    icon: "fa-solid fa-database"
    items:
      - name: "Link 3"
        logo: "{logo}"
        subtitle: "subtitle"
        tag: "tag"
        keywords: " "
        url: "https://domain.com/"
        target: "_blank" # optional html a tag target attribute

      - name: "Link 4"
        logo: "{logo}"
        subtitle: "subtitle"
        tag: "tag"
        keywords: " "
        url: "https://domain.com/"
        target: "_blank" # optional html a tag target attribute
        
```


More Information about the container and how to configure it can be found at:
- [Dockerhub](https://hub.docker.com/r/b4bz/homer)
- [Awesome Open Source](https://shownotes.opensourceisawesome.com/running-homer-dashboard/)
- [TheDigitalLifeTech](https://www.youtube.com/watch?v=9iTPm45EmxM)
