Scrutiny is a WebUI for smartd S.M.A.R.T monitoring.
It is currently run on [[Backup Instance (Deprecated)]] and proxied via [[Traefik]] on the [[Arm Instance]] (see [[Traefik]]).
It is run via [[Docker]]:
```yml
version: '3.5'

services:
  scrutiny:
    container_name: scrutiny
    image: ghcr.io/analogj/scrutiny:master-omnibus
    cap_add:
      - SYS_RAWIO
    ports:
      - "8201:8080" # webapp
      - "8086:8086" # influxDB admin
    volumes:
      - /run/udev:/run/udev:ro
      - ./config:/opt/scrutiny/config
      - ./influxdb:/opt/scrutiny/influxdb
    devices:
      - "/dev/sda"
      - "/dev/sdb"
      - "/dev/sdc"
      - "/dev/sdd"
```


More Information about the container and how to configure it can be found at:
- [GitHub](https://github.com/AnalogJ/scrutiny#docker)
