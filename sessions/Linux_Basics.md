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

Step 3: Enable the XRDP Service, and ensure that it runs at boot.

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



## Linux Permissions

User ibmsys1 is the zpdt owner. The directories where the z/OS volumes are deployed, and the files within them should all have owner "ibmsys1" and group "zpdt".

Looking at the directories under the /home/ibmsys1 folder we find that the owner and group are root, because I created them as root.
```
[root@localhost ibmsys1]# ls -al

...
drwxrwxr-x.  2 ibmsys1 neale 4096 Feb  1 14:14 zpdt2025
drwxr-xr-x.  2 root    root  4096 Apr 15 19:34 zpdteduk

```

Execute the following commands as root
```
chgrp zpdt zpdteduk
chown ibmsys1 zpdteduk
```

to see the following listing
```
drwxrwxr-x.  2 ibmsys1 neale 4096 Feb  1 14:14 zpdt2025
drwxr-xr-x.  2 ibmsys1 zpdt  4096 Apr 15 19:34 zpdteduk
```

Now, within /home/ibmsys1/zpdteduk do the same for the volumes.

Execute the following commands as root
```
chgrp zpdt *
chown ibmsys1 *
```
 to see the following
 ```
[root@localhost zpdteduk]# ls -al
total 295998240
drwxr-xr-x.  2 ibmsys1 zpdt        4096 Apr 15 19:39 .
drwxrwxrwx. 15 ibmsys1 zpdt        4096 Apr 15 18:47 ..
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:28 c3blz1
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:22 c3c610
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:23 c3c620
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:24 c3cfg1
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:22 c3dbar
-rwxrwxrwx.  1 ibmsys1 zpdt 15344640512 Apr 15 19:21 c3dbd1
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:21 c3dbd2
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:19 c3dis1
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:20 c3dis2
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:20 c3dis3
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:23 c3imf1
-rwxrwxrwx.  1 ibmsys1 zpdt 17049600512 Apr 15 19:29 c3inm1
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:28 c3kan1
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:16 c3paga
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:16 c3pagb
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:16 c3pagc
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:17 c3prd1
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:17 c3prd2
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:18 c3prd3
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:18 c3prd4
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:19 c3prd5
-rw-r--r--.  1 ibmsys1 zpdt  8539292672 Apr 15 19:39 c3res1
-rwxrwxrwx.  1 ibmsys1 zpdt   918356776 Apr 15 19:34 c3res1.zPDT
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:23 c3res2
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:26 c3sys1
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:24 c3usr1
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:25 c3uss1
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:25 c3uss2
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:26 c3uss3
-rwxrwxrwx.  1 ibmsys1 zpdt 15344640512 Apr 15 19:27 c3w901
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:27 c3w902
-rwxrwxrwx.  1 ibmsys1 zpdt 15344640512 Apr 15 19:30 c3zcx1
-rwxrwxrwx.  1 ibmsys1 zpdt  8539292672 Apr 15 19:31 c3zwe1
-rwxrwxrwx.  1 ibmsys1 zpdt          27 Apr 15 18:58 go.sh
-rwxrwxrwx.  1 ibmsys1 zpdt        2295 Apr 15 18:49 unpack.sh
-rw-r--r--.  1 ibmsys1 zpdt        3030 Apr 15 18:58 z31c_devmap
 ```


