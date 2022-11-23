# Linux
An operating system widel used in the server space. My server run ubuntu a special distro of linux. These systems can be accessed via ssh. ssh uses port 22 although this can be changed.

## important commands

### auto updates

```bash
apt install unattended-upgrades -y

dpkg-reconfigure --priority=low unattended-upgrades
```

### grand sudo

```bash 
adduser {user}

usermod -aG sudo {user}
```

### secure logins

```
nano /etc/ssh/sshd_config
```

### ssh keys

In the server:
```bash
mkdir ~/.ssh && chmod 700 ~/.ssh
```

In windows:
```shell
ssh-keygen -b 4096

scp $env:USERPROFILE/.ssh/id_rsa.pub {user}@{server_ip}:~/.ssh/authorized_keys
```

### networking

show used ports
```
ss -tupln
```


### filesystem

Change directory
```bash
cd /new/path
```

Parent folder
```bash
cd ..
```

Copy-Paste
```bash
cp /path/to/origin /path/to/new/location
```

Move
```bash
mv /path/to/origin /path/to/new/location
```

Delete file
```bash
rm filename
```

Delete folder
```bash
rm -r foldername
```

List Items
```bash
ls
```

Create a shortcut (mount folder to different folder)
```bash
mount -o bind /path/to/origin /path/to/new/shortcut
```

### permissions

Give a user access to a file:
```bash
sudo chown -R tuser:group /path/to/file
```

Make a file accessible by all users:
```bash
sudo chmod -R 777 /path/to/file
```

Permissions on ssh related things:
```sh
sudo chmod 700 ~/.ssh # for ssh directory
sudo chmod 644 ~/.ssh/public-key.pub # for public key
sudo chmod 600 ~/.ssh/id_rsa # for private key
sudo chmod 755 ~ # for home directory
```