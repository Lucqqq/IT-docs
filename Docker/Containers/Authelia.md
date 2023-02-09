# Authelia

Authelia is a middleware use by Traefik. Its purpose is to act as an additional auth layer. This means every access to a service via Traefik is first routed to Authelia where the user needs to authenticate. Authelia support 2FA and is configured to block any traffic to unknown subdomains. This adds additional security for your web-services.

![image](https://user-images.githubusercontent.com/108678440/217818225-40b13b18-155e-4d86-83af-7ad39080796f.png)


Authelia is set up in three different files:

First the Docker-Compose.yml:
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
jwt_secret: {secret} #a very secure string
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
  secret: {secret} #a very secure string
  expiration: 18000
  inactivity: 1800
  domain: domain.com  # Should match whatever your root protected domain is

regulation:
  max_retries: 5
  find_time: 120
  ban_time: 300

storage:
  encryption_key: {key} #a very secure string
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

## Setup

First, you need to create a folders and files like this:
```sh
mkdir authelia #create a authelia folder

cd authelia #enter the folder

touch docker-compose.yml #create a docker-compose file

mkdir config #create a config folder

cd config #enter the folder

touch configuration.yml #create a configuration file

touch users_database.yml #create a user-database
```

After that is done, you need to configure the configuration file. An example can be found above.
**Be sure to edit all the secret!!**
```sh
nano configuration.yml
```

Now you need to edit the user-database as seen above. 
```sh
nano users_database.yml
```
Since you can't just save your password directly in the file, you need to generate a hash of the password.
This can easily be done by running:
```sh 
docker run authelia/authelia:latest authelia hash-password 'yourpassword'
```
This docker-container will return the hash of the password.

After the user-database and the configuration file a setup, you need to configure the docker-compose file as seen above:
```sh
cd ..
nano docker-compose.yml
```
Be sure to edit it for your needs.

Now everything should be setup and you are able to start the container:
```sh
sudo docker-compose up -d
```
