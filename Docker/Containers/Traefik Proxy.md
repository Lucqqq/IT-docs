Compose File:
```yml
version: '3'

services:
  traefik:
    image: traefik:latest
    container_name: traefik
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true
    networks:
      - proxy
    ports:
	  - 32400:32400
      - 80:80
      - 443:443
    environment:
      - CF_API_EMAIL={your_mail}
      - CF_API_KEY={secret}
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - /path/to/traefik.yml:/traefik.yml:ro
      - /path/to/acme.json:/acme.json
      - /path/to/config.yml:/config.yml:ro
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.traefik.entrypoints=http"
      - "traefik.http.routers.traefik.rule=Host(`traefik.domain.com`)"
      - "traefik.http.middlewares.traefik-auth.basicauth.users={secret}"
      - "traefik.http.middlewares.traefik-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.middlewares.sslheader.headers.customrequestheaders.X-Forwarded-Proto=https"
      - "traefik.http.routers.traefik.middlewares=traefik-https-redirect"
      - "traefik.http.routers.traefik-secure.entrypoints=https"
      - "traefik.http.routers.traefik-secure.rule=Host(`traefik.domain.com`)"
      - "traefik.http.routers.traefik-secure.middlewares=traefik-auth"
      - "traefik.http.routers.traefik-secure.tls=true"
      - "traefik.http.routers.traefik-secure.tls.certresolver=cloudflare"
      - "traefik.http.routers.traefik-secure.tls.domains[0].main=domain.com"
      - "traefik.http.routers.traefik-secure.tls.domains[0].sans=*.domain.com"
      - "traefik.http.routers.traefik-secure.service=api@internal"

networks:
  proxy:
    external: true
```
config.yml File:
```yml
tcp:
  routers:
    plex:
      entryPoints:
        - "plex"
      service: plex
      rule: "HostSNI(`*`)"
    selfhost:
      entryPoints:
        - "https"
      service: selfhost
      rule: "HostSNI(`*`)"
  services:
    plex:
      loadBalancer:
        servers:
          - address: "10.8.0.3:32400"
    selfhost:
      loadBalancer:
        servers:
          - address: "10.8.0.3:443"
```
acme.json File:
```json
{
  "cloudflare": {
    "Account": {
      "Email": "{your_mail}",
      "Registration": {
        "body": {
          "status": "valid",
          "contact": [
            "mailto:{your_mail}"
          ]
        },
        "uri": "https://acme-v02.api.letsencrypt.org/acme/acct/{your_id}"
      },
      "PrivateKey": "{secret}      "KeyType": "4096"
    },
    "Certificates": null
  }
}
```
traefik.yml File:
```yml
api:
  dashboard: true
  debug: true
entryPoints:
  http:
    address: ":80"
  https:
    address: ":443"
  plex:
    address: ":32400"
serversTransport:
  insecureSkipVerify: true
providers:
  file:
    filename: /config.yml
certificatesResolvers:
  cloudflare:
    acme:
      email: {your_mail}
      storage: acme.json
      dnsChallenge:
        provider: cloudflare
        resolvers:
          - "1.1.1.1:53"
          - "1.0.0.1:53"    
```