# JDownloader

JDownloader is a headless [[Docker]]-Container. This means that the container itself does not offer any UI. Instead it relys on [my.jdownloader.org](https://my.jdownloader.org) to serve the UI. The environment-varibales define the account over which you can access the UI.
The purpose of this container is many to scrape an download video files from websites. It is setup on [[Arm Instance]] and saves the file via SFTP on [[Self-Hosted]]-Instance. 

The setup in the [[Docker-Compose]] file looks like this:
```yml
version: "3"

services:
  jdownloader:
    image: antlafarge/jdownloader:latest
    container_name: jdownloader # optional
    restart: on-failure:10 # optional
    user: 0:0 # optional
    volumes:
      - "/home/ubuntu/sftp/downloads:/jdownloader/downloads"
      - "/home/ubuntu/docker/jdownloader/config:/jdownloader/cfg" # optional
    environment:
      - "JD_EMAIL=your@mail.com"
      - "JD_PASSWORD=secret"
      - "JD_DEVICENAME=JD-DOCKER" # optional
    ports:
      - "3129:3129" # optional
```
