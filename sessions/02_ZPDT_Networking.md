# ZPDT Networking

Running z/OS under linux is a great platform to learn z/OS.
However, if you want to use it to deliver customer demos, then you need to think about networking.
This document describes 3 options for networking.

## Level 0 - Tunnel Network Only

When you install ZPDT under linux, you can run a 3270 emulator on the same linux system. It connects to z/OS using a TCPIP tunnel.

The TCPIP tunnel is a ZPDT construct to allow you to 
* access z/OS locally from a linux application (eg: 3270 emulator, or web browser )
* make external network access from z/OS (eg: to access ShopZ)

![network_l0](/sessions/images/network_l0.JPG)


## level 1 - Home LAN Networking

You may need broader network access. The following use cases come to mind.
* You may want to give customer demos via a Microsoft Teams meeting. This will need a Windows or Mac laptop to connect to Teams, and connect to the ZPDT.
* You may want to build demos with other systems on your home network. For example CDC replication from Db2 z/OS on ZPDT to Kafka on another computer.
* You may want to use development tools like VSCODE on Windows with zowe and other plugins.

In these curcumstances, you will want to configure the Linux host as one TCPIP address (eg: 192.168.1.170 ) and the z/OS guest as another ( eg: 192.168.1.171 ) as shown in the image below.

![network_l1](/sessions/images/network_l1.JPG)

## Level 2 - Remote Access

You'll want to leave your ZPDT system running 24*7, and you won't want to lug it to customer sites.  So how do you connect to your ZPDT when on the road ?

The image below show's Neale's solution.

![network_l2](/sessions/images/network_l2.JPG)

### Neale's solution
Most internet service providers don't give you a static IP addrees. So you need to configure a Dynamic DNS Service.
* NOIP is a cheap service ( $2 a month ) that lets you configure a static hostname, and map it to whatever IP address your ISP assigns to your router.
* You need to have a router that supports DDNS configuration. ( eg: Netgear Orbi ). You router will be responsibrl for telling your DDNS service where it is.
* You also need to configure Port Forwarding on your router. I configure my Orbi router to route anything for port 51820 to my Raspberry Pi on 192.168.1.66
* I install a Wireguard Server on my Raspberry Pi, and generate certificates for clients to connect securely from the internet to my Wireguard Server.
* I install the Wireguard client on my laptop, and define a VPN connection using the certificate to place my laptop on my home network.

The whole process is documented [here](https://pimylifeup.com/raspberry-pi-wireguard/).


### Ben's Solution
Ben has a more expensive router with cool features. Ben will document his approach here ....
