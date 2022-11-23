# Arm Instance
This instance is a virtual server located within the [[Oracle-Cloud]] and runs ubuntu 22.04 ([[Linux]]). It is a arm based system, consits of four v-cores and 24 GB of ram and has a 4 GiBit network connection.

## Startup:
Connect SFTP:
```sh
sshfs -o allow_other -p 2233 sftp@10.8.0.3:/sftp/downloads/ /home/ubuntu/sftp/downloads/
```

## Main purposes:
The Server has two main purposes:

### VPN
The first purpose is to serves a VPN  using OpenVPN.
It was setup using this [script](https://github.com/Nyr/openvpn-install) and this [tutorial](https://notthebe.ee/blog/creating-your-own-vpn/).
#### Adding a new client
To add a new client simply rerun the script which is already on the server and fill in the nformation. Once complete you need to move the generated config file to your client.
On your client you need to install the OpenVPN client and add the server using the configuration file.
Now you should be ready to use the VPN-server.

### Proxy
The second purpose is to server as a reverse-proxy for the [[Self-Hosted]] instance.
This was achieved using [[Traefik]]. The full documentation can be found at [[Traefik Proxy]], but here's a short summary:
All Request head to this instance. Traefik then routes them through the VPN and to [[Self-Hosted]].

## Secondary purpose:
The secondary purpose of the instance is to server as a downloader for TV-Series and movies. This is neccesary due to legal restictions in my homecountry.
The Downloader is setup using [[JDownloader]]. To automatically save everything on [[Self-Hosted]], this instance is connected to [[Self-Hosted]] via [[SFTP]] and the SFTPlocation is set as the default downloadpath.
