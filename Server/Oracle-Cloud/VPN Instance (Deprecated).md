# VPN Instance
A virtual Server located within the [[Oracle-Cloud]] and runs ubuntu 22.04 ([[Linux]]). It consits of two v-cores, 1 GB of memory and about 50 GB of storage.
It serves a VPN node where I can connect to using OpenVPN.
It was setup using this [script](https://github.com/Nyr/openvpn-install) and this [tutorial](https://notthebe.ee/blog/creating-your-own-vpn/).

### Adding a new client
To add a new client simply rerun the script which is already on the server and fill in the nformation. Once complete you need to move the generated config file to your client.
On your client you need to install the OpenVPN client and add the server using the configuration file.
Now you should be ready to use the VPN-server.
