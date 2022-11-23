# DashDot

DashDot is a simple dashboard showing the gerneral metrics of your machine.
These metrics include information about:
- CPU
- Memory
- Storage
- Networkspeed
DashDot is running within a [[Docker]]-Container and setup with the following [[Docker-Compose]] file:
```yml
version: '3.5'

services:
  dash:
    image: mauricenino/dashdot:latest
    restart: unless-stopped
    privileged: true
    ports:
      - '3001:3001'
    volumes:
      - /:/mnt/host:ro
    environment:
      DASHDOT_ENABLE_CPU_TEMPS: "true"
      DASHDOT_ENABLE_STORAGE_SPLIT_VIEW: "true"
      DASHDOT_FS_DEVICE_FILTER: sda,sdb,sdd
      DASHDOT_FS_VIRTUAL_MOUNTS: /dev/md0
      DASHDOT_WIDGET_LIST: os,cpu,storage,ram,network
      DASHDOT_STORAGE_LABEL_LIST: brand,size,type
      DASHDOT_RAM_LABEL_LIST: size,type,frequency
      DASHDOT_OVERRIDE_RAM_TYPE: DDR3
```
