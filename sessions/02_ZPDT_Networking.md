# ZPDT Networking

Running z/OS under linux is a great platform to learn z/OS.
However, if you want to use it to deliver customer demos, then you need to think about networking.
This document describes 3 options for networking.

## Level 0 - Tunnel Network Only

When you install ZPDT under linux, you can run a 3270 emulator on the same linux system. It connects to z/OS using a TCPIP tunnel.

The TCPIP tunnel is a ZPDT construct to allow you to access z/OS locally.

![network_l0](/sessions/images/network_l0.JPG)


## level 1 - Home LAN Networking

![network_l1](/sessions/images/network_l1.JPG)

## Level 2 - Remote Access


![network_l2](/sessions/images/network_l2.JPG)
