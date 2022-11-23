# Main Server Instance
This Server is a cloud based VM hosted by [Strato](https://www.strato.de). 
It has 8 cores of an "AMD EPYC 7453", a total of 16 GB memory and about 1.5 TB of storage.
Futher it is running Ubuntu 20.04 ([[Linux]]).

## Networking
The system is using ufw as a firewall, but due to conficts between [[Docker]] and ufw, a special procedure is necessary.
Within the file `/etc/ufw/after.rules` there was the following code added:
```rules
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
```bash
sudo systemctl restart ufw 
```
Now ufw and [[Docker]] are working togehter.

### To open a port just use:
```bash
ufw route allow proto tcp from any to any port 80
```
Note that the port you open has to be the internal port of the [[Docker]]-Container not the port on the host system!

**Warning:** this opens port 80 to any [[Docker]]-Container. To open only the port of a specific container please use:
```bash
ufw route allow proto tcp from any to 172.17.0.2 port 80
```
with 172.17.0.2 beeing the internal IP of the container.

## Services
This Server is curently running the following services:
- [[Docker]]
- [[Traefik]] ([[Docker]])
- [[Portainer]] ([[Docker]])
- [[Speedtest-Tracker]] ([[Docker]])
- [[Nextcloud]] ([[Docker]])
- [[Plex]] ([[Docker]])
