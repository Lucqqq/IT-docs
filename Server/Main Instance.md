# Main Server Instance
This Server is a cloud based VM hosted by [Strato](https://www.strato.de). 
It has 8 cores of an "AMD EPYC 7453", a total of 16 GB memory and about 1.5 TB of storage.
Futher it is running Ubuntu 20.04 ([[Linux]]).

## Startup:

Mount Webdav:
```sh
sudo sudo mount -t davfs -o noexec https://nextcloud.lucq.de/remote.php/dav/files/lucq/ /mnt/dav/
```

Start the sync:
```sh
screen -rd

sudo sh sync.sh
```

