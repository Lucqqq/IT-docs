Endlessh is an SSH tarp that very slowly sends an endless, random SSH banner. It keeps SSH clients locked up for hours or even days at a time. The purpose is to put your real SSH server on another port and then let the script kiddies get stuck in this tarp instead of bothering a real server.
It is currently run on [[Main Instance]].
It is run via [[Docker]]:
```yml
version: "2.1"

services:
  endlessh:
    image: lscr.io/linuxserver/endlessh:latest
    container_name: endlessh
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Berlin
      - MSDELAY=10000 #optional
      - MAXLINES=32 #optional
      - MAXCLIENTS=4096 #optional
      - LOGFILE=false #optional
      - BINDFAMILY= #optional
    volumes:
      - /path/to/config:/config #optional
    ports:
      - 22:2222
    restart: unless-stopped
```

More Information about the container and how to configure it can be found at:
- [DockerHub](https://hub.docker.com/r/linuxserver/endlessh)
