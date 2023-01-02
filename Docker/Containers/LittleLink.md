# LittleLink

LittleLink is a self-hosted version of [linktr.ee](https://linktr.ee). It is run within a [[Docker]]-Container and is set up using the following [[Docker-Compose]]-file:
```yml
version: "3.0"
services:
  littlelink-server:
    image: ghcr.io/techno-tim/littlelink-server:latest
    # dockerhub is also supported timothystewart6/littlelink-server
    # image: timothystewart6/littlelink-server:latest
    container_name: littlelink-server
    environment:
      - META_TITLE={name}
      - META_DESCRIPTION={description}
      - META_AUTHOR={name}
      - META_KEYWORDS={keywords}
      - LANG=en
      - META_INDEX_STATUS=all
      - OG_SITE_NAME={name}
      - OG_TITLE={name}
      - OG_DESCRIPTION=The home of {name}
      - OG_URL=https://domain.com
      - OG_IMAGE={image}
      - OG_IMAGE_WIDTH=400
      - OG_IMAGE_HEIGHT=400
      - GA_TRACKING_ID=G-XXXXXXXXXX
      - THEME=Dark
      - FAVICON_URL={image}
      - AVATAR_URL={image}
      - AVATAR_2X_URL={image}
      - AVATAR_ALT={name} Profile Pic
      - NAME={name}
      - BIO=Hey! Just a place where you can connect with me!
      # use ENV variable names for order, listed buttons will be boosted to the top
      - BUTTON_ORDER=STEAM,DISCORD,GITHUB,REDDIT,SPOTIFY,TWITTER,INSTAGRAM,TIKTOK,SNAPCHAT
      # you can render an unlimited amount of custom buttons by adding 
      # the CUSTOM_BUTTON_* variables and by using a comma as a separator.
      - GITHUB=https://github.com/{your_id}
      - TWITTER=https://twitter.com/{your_id}
      - INSTAGRAM=https://www.instagram.com/{your_id}/
      - DISCORD=https://discordapp.com/users/{your_id}/
      - TIKTOK=https://www.tiktok.com/@{your_id}
      - SNAPCHAT=https://www.snapchat.com/add/{your_id}
      - SPOTIFY=https://open.spotify.com/user/{your_id}
      - REDDIT=https://www.reddit.com/user/{your_id}
      - STEAM=https://steamcommunity.com/id/{your_id}/
      - FOOTER=See ya soon!

    restart: unless-stopped
    security_opt:
      - no-new-privileges:true
    networks:
      - proxy
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.littlelink-server.entrypoints=http"
      - "traefik.http.routers.littlelink-server.rule=Host(`info.domain.com`)"
      - "traefik.http.middlewares.littlelink-server-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.littlelink-server.middlewares=littlelink-server-https-redirect"
      - "traefik.http.routers.littlelink-server-secure.entrypoints=https"
      - "traefik.http.routers.littlelink-server-secure.rule=Host(`info.domain.com`)"
      - "traefik.http.routers.littlelink-server-secure.tls=true"
      - "traefik.http.routers.littlelink-server-secure.service=littlelink-server"
      - "traefik.http.services.littlelink-server.loadbalancer.server.port=3000"
      - "traefik.docker.network=proxy"
      - 'traefik.http.routers.littlelink-secure.middlewares=authelia@docker'

networks:
  proxy:
    external: true
```