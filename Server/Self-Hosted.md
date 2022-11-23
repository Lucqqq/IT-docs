# Self-Hosted

This Server is replacing [[Backup Instance (Depricated)]].

The Setup I would chose:

| Part | Choise |
|------|--------|
| CPU | Old I5 2320 |
| Mainboard | came with CPU |
| Memory | 8 GB DDR4 |
| Power-Supply | 450 Watt PSU (Bomb) |
| Case | old one |
| Storage Drive 1 | [Seagate IronWolf 4TB](https://geizhals.de/seagate-ironwolf-nas-hdd-rescue-4tb-st4000vn006-a2674664.html?hloc=at&hloc=de) |
| Storage Drive 2 | [Seagate IronWolf 4TB](https://geizhals.de/seagate-ironwolf-nas-hdd-rescue-4tb-st4000vn006-a2674664.html?hloc=at&hloc=de) |

## Startup:

Prepare the SFTP connection:

```sh
sudo mount -o bind /mnt/md1/media/plex/downloads /home/sftp/downloads
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