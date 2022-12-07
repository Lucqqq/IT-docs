# Backup Instance

This Server is self-hosted.
It is base on an old office pc and consists of the following hardware:

* Intel core I5 2320
* 4x 2GB DDR3 1333
* GeForce GT 620
* 3x 500GB HDD
* 128GB SSD The HDDs are configured in a Raid 5 and build a useable space of around 950GB. The Server itself is running [[Linux]] Ubuntu 20.04.

## Startup

These are the things that need to be run upon startup of the server:

Connecting to the filesystem of [[Main Instance]]:

```
sshfs -o allow_other -p {Port} {User}@{IP}:/ /mnt/sshftp -o IdentityFile=/home/lucq/.ssh/id_rsa
```

Starting the synchronisation of the backup:

```
screen -rd

sudo rsync -avu --delete "/mnt/sshftp/sftp/plex/" "/mnt/md0/backup/plex"
```

## Open VPN (external access)

Since the server is self-hosted it is located behind a NAT firewall by the local internetporvider.
Due to this it is not possible to access the server over the public internet.

To solve this the Server is connecetd to the VPN setup on the [[VPN Instance]].
There simply was added a new client to the VPN. But the resulting config file needed one addition.
In the top part of the file the following line was added:

```
pull-filter ignore redirect-gateway
```

This allows the server to be part of the VPN without forceing all traffic through the VPN.
Therefore it still uses its normal networkconnection but is accessible through the VPN.

To connect the server to the VPN simply intall openvpn and add the edited config file to the folder "/etc/openvpn/".

This allows access to the server over the vpn but to publicly access its web-services a few things are neccesary:

* The web-service need to be exposed to a port (see Networking).
* A server with Traefik running needs to be connected to the VPN ([[Arm Instance]])
* This server ([[Arm Instance]]) will now proxy all incomming requests over the VPN to the web-service (see [[Traefik]] for config)

## Networking

The system is using ufw as a firewall, but due to conficts between [[Docker]] and ufw, a special procedure is necessary.
Within the file `/etc/ufw/after.rules` there was the following code added:

```
# BEGIN UFW AND DOCKER
*filter
:ufw-user-forward - [0:0]
:ufw-docker-logging-deny - [0:0]
:DOCKER-USER - [0:0]
-A DOCKER-USER -j ufw-user-forward

-A DOCKER-USER -j RETURN -s 10.0.0.0/8
-A DOCKER-USER -j RETURN -s 172.16.0.0/12
-A DOCKER-USER -j RETURN -s 192.168.0.0/16

-A DOCKER-USER -p udp -m udp --sport 53 --dport 1024:65535 -j RETURN

-A DOCKER-USER -j ufw-docker-logging-deny -p tcp -m tcp --tcp-flags FIN,SYN,RST,ACK SYN -d 192.168.0.0/16
-A DOCKER-USER -j ufw-docker-logging-deny -p tcp -m tcp --tcp-flags FIN,SYN,RST,ACK SYN -d 10.0.0.0/8
-A DOCKER-USER -j ufw-docker-logging-deny -p tcp -m tcp --tcp-flags FIN,SYN,RST,ACK SYN -d 172.16.0.0/12
-A DOCKER-USER -j ufw-docker-logging-deny -p udp -m udp --dport 0:32767 -d 192.168.0.0/16
-A DOCKER-USER -j ufw-docker-logging-deny -p udp -m udp --dport 0:32767 -d 10.0.0.0/8
-A DOCKER-USER -j ufw-docker-logging-deny -p udp -m udp --dport 0:32767 -d 172.16.0.0/12

-A DOCKER-USER -j RETURN

-A ufw-docker-logging-deny -m limit --limit 3/min --limit-burst 10 -j LOG --log-prefix "[UFW DOCKER BLOCK] "
-A ufw-docker-logging-deny -j DROP

COMMIT
# END UFW AND DOCKER
```

After that ufw was restarted:

```
sudo systemctl restart ufw 
```

Now ufw and [[Docker]] are working togehter.

### To open a port just use:

```
ufw route allow proto tcp from any to any port 80
```

Note that the port you open has to be the internal port of the [[Docker]]\-Container not the port on the host system!

**Warning:** this opens port 80 to any [[Docker]]\-Container. To open only the port of a specific container please use:

```
ufw route allow proto tcp from any to 172.17.0.2 port 80
```

with 172.17.0.2 beeing the internal IP of the container.

## Backups

This is currently the main task of this server. It is backing mainly video files from [[Main Instance]] up.
These files are saved on an Array of three drives.

### Dirve Array

This is the array where the backups get saved. It consists of three HDD each with 500GB storage space. These drives are setup in an raid array. This allows for the faliure of one drive without the lost of any files.

Raid Setup:
Finding the names of the drives to use:

```
lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINT
```

Output:

```
OutputNAME     SIZE FSTYPE   TYPE MOUNTPOINT
sda      100G          disk 
sdb      100G          disk 
sdc      100G          disk 
```

Creating the array:

```
sudo mdadm --create --verbose /dev/{array_name} --level=5 --raid-devices=3 /dev/{disk1_name} /dev/{disk2name} /dev/{disk3_name}
```

Formating the array:

```
sudo mkfs.ext4 -F /dev/{array_name}
```

Creating a mountpoint:

```
sudo mkdir -p /place/to/mount/to/
```

Mounting the array:

```
sudo mount /dev/{array_name} /place/to/mount/to/
```

Check if the array is available:

```
df -h -x devtmpfs -x tmpfs
```

Automatically mounting the array:

```
sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf

sudo update-initramfs -u

echo '/dev/{array_name} /place/to/mount/to/ ext4 defaults,nofail,discard 0 0' | sudo tee -a /etc/fstab
```

In the case of this server the three drives create an array with 931GB of storage. This array is mounted at "/mnt/md0/".

### External source via sftp

To backup files of a different system it is necessary to connect to the system via [[SFTP]].
To connect to the system via [[SFTP]] and mount it somewhere in your local filesystem simply use:

```
sshfs -o allow_other -p {Port} {User}@{IP}:/ /path/to/mount/to -o IdentityFile=/path/to/ssh_key
```

### Starting the Backup

First create a new screen to run our backup in. This allows to later check on the progress or pause the backup etc.

```
screen
```

Start of the backup within this new screen:

```
sudo rsync -avu --delete "/path/to/source/" "/path/to/backup"
```

This will automatically sync the backup folder to be the same as the source folder.
To exit the screen press "ctrl" + "A" + "D".
To later check on the synchronisation progess or to pause synchronisation:

```
screen -rd
```

To stop the synchronisation now press "ctrl" + "C"