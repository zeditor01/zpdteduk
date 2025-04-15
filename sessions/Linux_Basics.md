# Linux Basics

this section assumes that you have installed Linux, assigned a fixed TCPIP address, and created a logon userid and password.

## Starting Linux Apps

After logging on to CentOS Linux, you can open apps by clicking on the "Activities" menu on the top-left of the screen. This will bring up a menu like the one below.

![activities](/sessions/images/activities.JPG)

The most useful apps are 
1. Firefox
2. File Manager
3. System Monoitor
4. Terminal
5. Disk settings
6. x3270 emulator.

## Opening a remote desktop.
For demos it's really handy to connect from your work laptop into the Linux and z/OS systems, so that you can share your screen via Teams. This needs you to install xrdp on linux, open the xrdp port in any firewalls, and open the Remote Desktop Connection app on your work laptop. The following steps is what you need to do.


Step 1: Enable the EPEL Repository. The XRDP package is available in the EPEL (Extra Packages for Enterprise Linux) repository. Enable it:

```
sudo dnf install epel-release -y
```

Step 2: Install XRDP. Install XRDP and its dependencies:
```
sudo dnf install xrdp tigervnc-server -y
```

Step 3: Enable the ZRDP Service, and ensure that it runs at boot.

```
sudo systemctl enable xrdp --now
```

Check the status of the XRDP Service
```
sudo systemctl status xrdp

```

Step 4: Configure the Firewall

All modern linux distros enable the SE Linux firewall by default. We need to open the XRDP port (default 3389) to allow RDP inbound connections

```
sudo firewall-cmd --add-port=3389/tcp --permanent
sudo firewall-cmd --reload
```

At this point i would shutdown and restart linux, before attempting to open an RDP session to my NUC.

Once Linux is booted, 
* from my Windows client I open the "Remote Desktop" application and connect to 192.168.1.170 
* If you run a Mac - Mac app store has a free RDP app from Microsoft.

![rdp01](/sessions/images/rdp01.JPG)

then I can access my Linux Desktop from Windows client, to perform IPLs and shutdowns.

![rdp02](/sessions/images/rdp02.JPG)





