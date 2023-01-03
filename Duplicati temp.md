```yaml
version: "2.1"
services:
  duplicati:
    image: lscr.io/linuxserver/duplicati:latest
    container_name: duplicati
    environment:
      - PUID=0
      - PGID=0
      - TZ=Europe/Berlin
    volumes:
      - /home/lucq/duplicati:/config
      - /path/to/backups:/backups
    ports:
      - 8200:8200
    restart: unless-stopped
```

