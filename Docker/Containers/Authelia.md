# Authelia

Authelia is a middleware use by [[Traefik]]. Its purpose is to act as an additional auth layer. This means every access to a service via [[Traefik]] is first routed to authelia where the user needs to authenticate. Authelia support 2FA and is configured to block any traefik to unknown subdomains. This adds additional security for your web-services.
Authelia is setup in three different files:

First the [[Docker-Compose]].yml:
```yml
version: '3'

services:
  authelia:
    image: authelia/authelia
    container_name: authelia
    volumes:
      - /path/to/config:/config
    networks:
      - proxy
    labels:
      - 'traefik.enable=true'
      - 'traefik.http.routers.authelia.rule=Host(`login.domain.com`)'
      - 'traefik.http.routers.authelia.entrypoints=https'
      - 'traefik.http.routers.authelia.tls=true'
      - 'traefik.http.middlewares.authelia.forwardauth.address=http://authelia:9091/api/verify?rd=https://login.domain.com'
      - 'traefik.http.middlewares.authelia.forwardauth.trustForwardHeader=true'
      - 'traefik.http.middlewares.authelia.forwardauth.authResponseHeaders=Remote-User,Remote-Groups,Remote-Name,Remote-Email'
    expose:
      - 9091
    restart: unless-stopped
    environment:
      - TZ=Europe/Berlin
    healthcheck:
      disable: true
networks:
  proxy:
    external: true
```

The two other files sit in the /config folder mapped in the docker-compose.yml

configuration.yml:
```yml
---
###############################################################
#                   Authelia configuration                    #
###############################################################

server:
  host: 0.0.0.0
  port: 9091
log:
  level: debug
theme: dark
# This secret can also be set using the env variables AUTHELIA_JWT_SECRET_FILE
jwt_secret: {secret}
default_redirection_url: https://login.domain.com
totp:
  issuer: authelia.com

authentication_backend:
  file:
    path: /config/users_database.yml
    password:
      algorithm: argon2id
      iterations: 1
      salt_length: 16
      parallelism: 8
      memory: 64

access_control:
  default_policy: deny
  rules:
    # Rules applied to everyone
    - domain: one.domain.com
      policy: bypass
    - domain: two.domain.com
      policy: two_factor


session:
  name: authelia_session
  # This secret can also be set using the env variables AUTHELIA_SESSION_SECRET_FILE
  secret: {secret}
  expiration: 18000
  inactivity: 1800
  domain: domain.com  # Should match whatever your root protected domain is

regulation:
  max_retries: 5
  find_time: 120
  ban_time: 300

storage:
  encryption_key: {key}
  local:
    path: /config/db.sqlite3

notifier:
  smtp:
    username: 
    password: 
    host: 
    port: 
    sender: 

ntp:
  address: "time.cloudflare.com:123"
  version: 3
  max_desync: 3s
  disable_startup_check: false
  disable_failure: true
...

```

users_database.yml
```yml
---
###############################################################
#                         Users Database                      #
###############################################################

# List of users
users:
  user:
    displayname: "Name"
    password: "secret"
    email: 
    groups:
      - admins
      - dev
...
```
