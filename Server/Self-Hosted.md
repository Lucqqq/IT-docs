# Self-Hosted

This Server is replacing [[Backup Instance (Deprecated)]].

The Setup I would chose:

| Part | Choise |
|------|--------|
| CPU | Old I5 2320 |
| Mainboard | came with CPU |
| Memory | 8 GB DDR3 |
| Power-Supply | 450 Watt PSU |
| Case | old one |
| Storage Drive 1 | [Seagate IronWolf 4TB](https://geizhals.de/seagate-ironwolf-nas-hdd-rescue-4tb-st4000vn006-a2674664.html?hloc=at&hloc=de) |
| Storage Drive 2 | [Seagate IronWolf 4TB](https://geizhals.de/seagate-ironwolf-nas-hdd-rescue-4tb-st4000vn006-a2674664.html?hloc=at&hloc=de) |

## Startup:

Prepare the SFTP connection:

```sh
sudo mount -o bind /mnt/md_1/media/plex/downloads /home/sftp/downloads
```

Connect SFTP from [[Arm Instance]]:

```sh
sshfs -o allow_other -p 2233 sftp@10.8.0.3:/sftp/downloads/ /home/ubuntu/sftp/downloads/
```

## SFTP

This is how I setup a SFTP-server on this Instance. A detailed tutorial can be found at [setup-sftp-server-ubuntu](https://linuxhint.com/setup-sftp-server-ubuntu/).

Create SFTP users group:

```
sudo addgroup sftp
```

Create a new SFTP user:

```
sudo useradd -m sftp_user -g sftp
```

Set the password for the new user:

```
sudo passwd sftp_user
```

Set the correct permissions for the user:

```
sudo chmod 700 /home/sftp_user/
```

Edit the current ssh configuration:

```
sudo nano /etc/ssh/sshd_config
```

Now you need to connect to the server as the sftp_user:

```
ssh sftp_user@{server.ip} -p {port}
```

Add this at the bottom of the file:

```
...

Match group sftp
ChrootDirectory /home
X11Forwarding no
AllowTcpForwarding no
ForceCommand internal-sftp
PasswordAuthentication yes
```

This will force users of the group sftp to use the SFTP-service.

Restart ssh:

```
sudo systemctl restart ssh
```

Now you should be able to login to the server using your password:

```
sftp sftp_user@{server.ip} -p {port}
```

To mount the sftp conection to a certain folder use this command:

```
sshfs -p {Port} {User}@{server.ip}:/ /path/to/mount/to
```

To revert this simply type:

```
sudo umount /path/to/mount/to
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

```sh
ufw route allow proto tcp from any to 172.17.0.2 port 80
```

with 172.17.0.2 beeing the internal IP of the container.

## Dirve Arrays

This is the array where the backups get saved. It consists of three HDD each with 500GB storage space. These drives are setup in an raid array. This allows for the faliure of one drive without the lost of any files.

Raid Setup:
Finding the names of the drives to use:

```sh
lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINT
```

Output:

```sh
OutputNAME     SIZE FSTYPE   TYPE MOUNTPOINT
sda      100G          disk 
sdb      100G          disk 
sdc      100G          disk 
```

Creating the array:

```sh
sudo mdadm --create --verbose /dev/{array_name} --level=1 --raid-devices=2 /dev/{disk1_name} /dev/{disk2name}
```

Formating the array:

```sh
sudo mkfs.ext4 -F /dev/{array_name}
```

Creating a mountpoint:

```sh
sudo mkdir -p /place/to/mount/to/
```

Mounting the array:

```sh
sudo mount /dev/{array_name} /place/to/mount/to/
```

Check if the array is available:

```sh
df -h -x devtmpfs -x tmpfs
```

Automatically mounting the array:

```sh
sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf

sudo update-initramfs -u

echo '/dev/{array_name} /place/to/mount/to/ ext4 defaults,nofail,discard 0 0' | sudo tee -a /etc/fstab
```

In the case of this server two drives create an array with 4TB of storage. This array is mounted at "/mnt/md_1/".
Additionally there is an backup-arrray consiting of two other drives. This array has a size of 465GB and is mounted to "/mnt/md0/".

## Swap

Since the Server only has 8GB of memory I increased the swap space from 4GB to 16GB.
This was acomplished the following way:

Check the current swap size:
```sh 
sudo swapon --show
```

Create a new swap file:
```sh
sudo fallocate -l 12G /swapfile
```

Check if the file was created with correct amount of space:
```sh
ls -lh /swapfile
```

Setting the corrent permissions on the swap file:
```sh
sudo chmod 600 /swapfile
```

Marking the file as swap storage:
```sh
sudo mkswap /swapfile
```

Enable the new swap:
```sh
sudo swapon /swapfile
```

Check if the swap is aviable:
```sh
sudo swapon --show
```

Backing up the "/etc/fstab" file before editing it:
```sh
sudo cp /etc/fstab /etc/fstab.bak
```

Adding the swap to the fstab file for automount on start:
```sh
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

