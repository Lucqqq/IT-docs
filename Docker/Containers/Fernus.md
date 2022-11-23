Fernus is a web-service which lets you create multiple dashboards to view all your services. It also has the abillity to connect to the api of certain other services e.g. nextcloud, plex.
Fernus is running within a [[Docker]]-Container and setup with the following [[Docker-Compose]] file:
```yml
version: "3"

services:
  fenrus:
    image: revenz/fenrus
    container_name: fenrus
    environment:
      - TZ=Pacific/Auckland
    volumes:
      - /path/to/data:/app/data
      - /path/to/images:/app/wwwroot/images
    ports:
      - 9500:3000
    restart: unless-stopped
```