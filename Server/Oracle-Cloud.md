# Oracle-Cloud

Oracle-Cloud is a cloud provider. All instances I use run [[Linux]]. Curently I have the following Instances running in the Oracle-Cloud infrastructure:

* [[Arm Instance]]
* [[VPN Instance]]

## Networking

Oracle-Cloud has a special for of network protecting your Servers. To open a Port on a Server you first have to login into [Oracle-Cloud](https://www.oracle.com/cloud/sign-in.html) (Firefox or Chrome), then you have to select the virtual network the instance is in. From there you have to open the default security list for said network. Here you will nee to add an "ingress rule" looking something like this:

![Add-Ingress-Rule](_assets/IngressRule.png)

### Deprecated:
Now the Server is accessible from the public internet. The firewall of the server on the other hand is still blocking all trafik. To fix this you need to follow these steps:

First you need to install and enable firewalld to easily edit your iptables:

```
sudo apt install firewalld
sudo systemctl enable firewalld
```

Note that ufw wont work!

Now you can open all desired Ports with the following command:

```
sudo firewall-cmd --permanent --zone=public --add-port={Port}/tcp
```

Next you need to reload firewalld to aply any changes:

```
sudo firewall-cmd --reload
```

Last but not least you will have to flush your iptables to see the changes in effect:

```
sudo iptables -t filter -F
sudo iptables -t filter -X
```

Now your server should be accessible on your desired Port.

In case you are trying to expose a Port of a [[Docker]]-Service you will have to restart docker to access it:

```
systemctl restart docker
```