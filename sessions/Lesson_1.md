# Lesson 1 - Build your ADCD z/OS image, IPL, configure TCPIP, Shutdown

## Contents 
1.1 Install the ZPDT application on Linux x86 
1.2 Download and Unpack the ADCD volumes
1.3 Edit Device Map and IPL Script
1.4 First IPL 
1.5 Navigate ISPF/PDF Menus
1.6 Edit TCPIP stack in z/OS 
1.7 Test TCPIP connectivity 
1.8 Shutdown z/OS 

## 1.1 Install the ZPDT application on Linux x86 


This section is structured as follows
1.1.1 Documentation Reference
1.1.2 Download the ZPDT Driver
1.1.3 Install the Linux pre-Requisites for ZPDT
1.1.4 Install the ZPDT Driver
1.1.5 Request the ZPDT License
1.1.6 Install the ZPDT License



### 1.1.1 Documentation

ISV IBM zPDT Guide and Reference (SG24-8205) can be downloaded from [here SG248205](https://www.redbooks.ibm.com/redbooks/pdfs/sg248205.pdf). This document is the definitive guide for setting up ZPDT. 


### 1.1.2 Download the ZPDT Driver

The IBM Resourcelink website is where you download the ZPDT Driver [IBM resourcelink](https://www.ibm.com/support/resourcelink/). Look down towards the bottom of the page to find <b>1090 Support.</b>


 <img src="/sessions/images/rl02.JPG" alt="rl02" width="70%">


The 1090 Support Page allows you to request the 1090 drivers ( which is what you need to install on your NUC ). It also contains links to license files that you have previously requested.

 <img src="/sessions/images/rl03.JPG" alt="rl03" width="70%">
 


Click on the Drivers link, and download the latest Linux 64bit driver 

 <img src="/sessions/images/rl04.JPG" alt="rl04" width="70%">
 



### 1.1.3 Install the Linux Pre-Requisites for ZPDT

You will need the x3270 package. the z/OS master console will be accessed using x3270. Some other pre-requisites must also be installed.

Open a linux terminal and install these packages. (you may need to prefix the commands with ```sudo``` to get root privilege.

```
# yum install libstdc++.i686 
# yum install libnsl
# yum install perl
# yum install x3270 (Optional. A 3270 emulator package.)
# yum install x3270-x11 (Needed by x3270.)
```

Next - we need to create the ibmsys1 userid, which will be the owner of the ZPDT instance.
It needs to be in a group, and you need to set a password. the commands below will do the trick

```
sudo groupadd -g 970 zpdt
sudo useradd -u 1070 -g zpdt -m -d /home/ibmsys1 ibmsys1
passwd ibmsys1
usermod -aG wheel ibmsys1
```

Edit the Linux kernel settings. ZPDT requires some kernal setting to be correct. Edit ```/etc/sysctl.conf``` using nano or some other editor, with the following values.

Command: ```sudo nano /etc/sysctl.conf```

```
kernel.core_pattern=core-%e-%p-%t
kernel.core_uses_pid=1
kernel.msgmax=65536
kernel.msgmnb=65536
kernel.msgmni=512 (Change for large number of devices.)
kernel.shmmax=36000000000
kernel.shmall=24000000
kernel.sem=250 32000 250 1024
net.core.rmem_max=1048576
net.core.rmem_default=1048576
```

Then activate the new kernel settings with the following command ( or reboot linux 0
```
# /sbin/sysctl -p /etc/sysctl.conf
```


Edit bashrc to set some path values.

Command: ```sudo nano /home/ibmsys1/.bashrc```


```
export PATH=/usr/z1090/bin:$PATH
export LD_LIBRARY_PATH=/usr/z1090/bin:$LD_LIBRARY_PATH
export MANPATH=/usr/z1090/man:$MANPATH
ulimit -c unlimited
ulimit -d unlimited
ulimit -m unlimited (If more than 128 emulated I/O devices)
ulimit -v unlimited (If more than 128 emulated I/O devices)
```

Finally you can test whether the system meets the pre-requisites for ZPDT.

open a terminal as root, change directory to /usr/z1090/bin, and execute z1090instcheck

![z1090check](/sessions/images/z1090check.JPG)

Oops - my system fails on one check, but it still works.

### 1.1.4 Install the ZPDT Driver

OK - so you downloaded the ZPDT driver (z1090-1-12.59.17.x86_64), and it's sitting in your Downloads directory. the screenshot below also includes all the ADCD zOS volume images.

If the ZPDT driver (z1090-1-12.59.17.x86_64) isn't executable, make it executable by issuing the command ```chmod 777 z1090-1-12.59.17.x86_64```.

![Downloads](/sessions/images/Downloads.JPG)

Now, su to root, and run the installer
```
su -

cd /home/neale/Downloads

./z1090-1-12.59.17.x86_64
```

### 1.1.5 Request the ZPDT License

Logon to linux, switch to the /usr/z1090/bin directory, and execute the request_license command

```
[ibmsys1@RHEL ~]$ cd /usr/z1090/bin
[ibmsys1@RHEL bin]$ sudo ./request_license
```
Using the firefox browser in your Linux PC, logon the [IBM resourcelink](https://www.ibm.com/support/resourcelink/) with your IBM id.

Choose lease extension, fill in the details, and upload your request file

![lease](/sessions/images/lease.JPG)

You must enter the serial number of your own ZPDT. the maximum date extension is 1 year.

### 1.1.6 Install the ZPDT License

The request process says to wait an hour, but it usually works immediately.

Download the license file.

Open a terminal, switch to /usr/z1090/bin and execute the update license command.

```
sudo ./update_license ~/zpdt7859019774907418906rhel_1719912758-update.zip
```


## 1.2 Download and Unpack the ADCD volumes

if you downloaded all the volumes from the ADCD website, your Downloads directory should have the following

```
[neale@localhost Downloads]$ ls -al c3*
-rw-r--r--. 1 neale neale 2590502242 Apr 11 17:21 c3blz1.gz
-rw-r--r--. 1 neale neale 1464274213 Apr 11 17:16 c3c610.gz
-rw-r--r--. 1 neale neale 1825183260 Apr 11 17:16 c3c620.gz
-rw-r--r--. 1 neale neale   10993131 Apr 11 17:10 c3cfg1.gz
-rw-r--r--. 1 neale neale   10231920 Apr 11 17:10 c3dbar.gz
-rw-r--r--. 1 neale neale 1541640644 Apr 11 17:12 c3dbd1.gz
-rw-r--r--. 1 neale neale   34085859 Apr 11 17:10 c3dbd2.gz
-rw-r--r--. 1 neale neale 2919995822 Apr 11 17:22 c3dis1.gz
-rw-r--r--. 1 neale neale 2555094347 Apr 11 17:20 c3dis2.gz
-rw-r--r--. 1 neale neale  497253034 Apr 11 17:01 c3dis3.gz
-rw-r--r--. 1 neale neale  950411343 Apr 11 16:40 c3imf1.gz
-rw-r--r--. 1 neale neale 9848829584 Apr 11 17:24 c3inm1.gz
-rw-r--r--. 1 neale neale 3617130537 Apr 11 16:53 c3kan1.gz
-rw-r--r--. 1 neale neale   49194517 Apr 11 16:32 c3paga.gz
-rw-r--r--. 1 neale neale   19639609 Apr 11 16:56 c3pagb.gz
-rw-r--r--. 1 neale neale   19639342 Apr 11 16:56 c3pagc.gz
-rw-r--r--. 1 neale neale 2456443439 Apr 11 16:48 c3prd1.gz
-rw-r--r--. 1 neale neale 3260218352 Apr 11 16:52 c3prd2.gz
-rw-r--r--. 1 neale neale 4173951332 Apr 11 16:34 c3prd3.gz
-rw-r--r--. 1 neale neale 3974631195 Apr 11 16:32 c3prd4.gz
-rw-r--r--. 1 neale neale 3377438646 Apr 11 16:30 c3prd5.gz
-rw-r--r--. 1 neale neale  918356776 Apr 11 16:15 c3res1.zPDT
-rw-r--r--. 1 neale neale  501222924 Apr 11 16:12 c3res2.gz
-rw-r--r--. 1 neale neale  280312561 Apr 11 15:49 c3sys1.gz
-rw-r--r--. 1 neale neale   10056628 Apr 11 16:31 c3usr1.gz
-rw-r--r--. 1 neale neale 1879410244 Apr 11 15:56 c3uss1.gz
-rw-r--r--. 1 neale neale 3766476768 Apr 11 16:30 c3uss2.gz
-rw-r--r--. 1 neale neale 1624579586 Apr 11 15:55 c3uss3.gz
-rw-r--r--. 1 neale neale 6027121183 Apr 11 16:02 c3w901.gz
-rw-r--r--. 1 neale neale  129562411 Apr 11 15:47 c3w902.gz
-rw-r--r--. 1 neale neale   38052571 Apr 11 15:46 c3zcx1.gz
-rw-r--r--. 1 neale neale 1193154025 Apr 11 15:52 c3zwe1.gz

```

Create a directory with plenty of space that you will run your z/OS image from. I tend to create a directory under the home directory of the ZPDT instance owner (ibmsys1).

I have two directories. 
* zpdt2025 is my "production" z/OS image with all my prepared installs and demos.
* zpdteduk is my "development" z/OS images, which is a fresh ADCD download that has not been customised.

```
[root@localhost ibmsys1]# pwd
/home/ibmsys1
[root@localhost ibmsys1]# ls -al
total 28
drwx------.  8 ibmsys1 zpdt 4096 Apr 15 13:02 .
drwxr-xr-x.  8 root    root   97 Mar 22 11:19 ..
-rw-r--r--.  1 ibmsys1 zpdt   18 Feb 16  2024 .bash_logout
-rw-r--r--.  1 ibmsys1 zpdt  141 Feb 16  2024 .bash_profile
-rw-r--r--.  1 ibmsys1 zpdt  707 Mar 22 11:23 .bashrc
drwx------.  2 ibmsys1 zpdt    6 Mar 23 15:22 .cache
drwxr-xr-x.  3 ibmsys1 zpdt   40 Mar 23 16:01 .hasplm
drwxr-xr-x.  4 ibmsys1 zpdt   39 Feb 14 08:24 .mozilla
-rw-r--r--.  1 ibmsys1 zpdt  151 Mar 23 17:08 .x3270connect
-rw-------.  1 ibmsys1 zpdt   67 Mar 23 15:22 .xauthCmQUpZ
drwxrwx---. 10 ibmsys1 zpdt  111 Mar 23 16:01 z1090
drwxr-xr-x.  2 root    root 4096 Mar 22 16:38 zpdt2025
drwxr-xr-x.  2 root    root    6 Apr 15 13:02 zpdteduk
```

Looking inside zpdt2025 I see all the volumes, fully decompressed. 

These are from an older ADCD distribution. the first ADCD release volumes were labelled a3xxxx, the second were labelled b3xxxx and the third volumes were labelled c3xxxx.

```
root@localhost zpdt2025]# ls -al
total 553443024
drwxr-xr-x. 2 root    root        4096 Mar 22 16:38 .
drwx------. 8 ibmsys1 zpdt        4096 Apr 15 13:02 ..
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 15 13:04 a3blz1
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 15 13:04 a3c560
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 15 13:04 a3c610
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 15 13:05 a3cfg1
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Dec 17  2023 a3dbar
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 15 13:04 a3dbc1
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Jan  4  2024 a3dbc2
-rwxrwxrwx. 1 ibmsys1 zpdt 15344640512 Apr 15 13:04 a3dbd1
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 15 13:03 a3dbd2
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 29  2024 a3dis1
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Dec 17  2023 a3dis2
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 29  2024 a3dis3
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Mar 23 17:12 a3imf1
-rwxrwxrwx. 1 ibmsys1 zpdt 17049600512 Mar 23 16:03 a3inm1
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Mar 23 16:03 a3kan1
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 15 13:03 a3paga
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 15 13:03 a3pagb
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 15 13:03 a3pagc
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr  2 15:12 a3prd1
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 15 13:04 a3prd2
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr  2 15:12 a3prd3
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 15 13:04 a3prd4
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 15 13:04 a3prd5
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 14 16:11 a3res1
-rwxrwxrwx. 1 ibmsys1 zpdt   781944152 Dec 17  2023 a3res1.zPDT
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr  2 15:12 a3res2
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 15 13:05 a3sys1
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 15 13:04 a3usr1
-rwxrwxrwx. 1 ibmsys1 zpdt 25617876992 Apr 15 13:04 a3usr2
-rwxrwxrwx. 1 ibmsys1 zpdt 25617876992 Apr 15 13:04 a3usr3
-rwxrwxrwx. 1 ibmsys1 zpdt 25617876992 Apr  2 17:03 a3usr4
-rwxrwxrwx. 1 ibmsys1 zpdt 25617876992 Apr 15 12:53 a3usr5
-rwxrwxrwx. 1 ibmsys1 zpdt 25617876992 Apr 10 08:12 a3usr6
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 15 13:04 a3uss1
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 15 13:04 a3uss2
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Mar 23 16:02 a3uss3
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Mar 23 16:02 a3w901
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 15 13:04 a3w902
-rwxrwxrwx. 1 ibmsys1 zpdt 15344640512 Apr 15 13:04 a3zcx1
-rwxrwxrwx. 1 ibmsys1 zpdt 25617876992 Mar 14  2024 a3zcx2
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr 24  2024 a3zwe1
-rwxrwxrwx. 1 ibmsys1 zpdt          22 Jan  7 19:58 awscheck.sh
-rwxrwxrwx. 1 ibmsys1 zpdt          27 Jan  7 16:01 go.sh
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Dec 18  2023 sares1
-rwxrwxrwx. 1 ibmsys1 zpdt   518925864 Dec 17  2023 sares1.zPDT
-rwxrwxrwx. 1 root    root          56 Mar 22 13:25 unpack.sh
-rwxrwxrwx. 1 ibmsys1 zpdt  8539292672 Apr  2 17:03 work0a
-rwxrwxrwx. 1 ibmsys1 zpdt        4101 Mar 22 16:40 z31a_devmap
```

you will also notice some other files that we will need to create.
* go.sh is my IPL script
* z31a_devmap is my device map
* work0a is a fresh work volume that I created
* a3usr2, a3usr3 and a3use4 are also fresh volumes that I created for installing new software.

The volumes can be unpacked one at a time using commands like the following

```
gunzip -c /home/neale/Downloads/c3blz1.gz > /home/ibmsys1/zpdteduk/c3blz1
```

Or you can create an executable script (unpack.sh) to unpack them all, one after the other.

```
gunzip -c /home/neale/Downloads/c3paga.gz > /home/ibmsys1/zpdteduk/c3paga
gunzip -c /home/neale/Downloads/c3pagb.gz > /home/ibmsys1/zpdteduk/c3pagb
gunzip -c /home/neale/Downloads/c3pagc.gz > /home/ibmsys1/zpdteduk/c3pagc
gunzip -c /home/neale/Downloads/c3prd1.gz > /home/ibmsys1/zpdteduk/c3prd1 
gunzip -c /home/neale/Downloads/c3prd2.gz > /home/ibmsys1/zpdteduk/c3prd2
gunzip -c /home/neale/Downloads/c3prd3.gz > /home/ibmsys1/zpdteduk/c3prd3
gunzip -c /home/neale/Downloads/c3prd4.gz > /home/ibmsys1/zpdteduk/c3prd4
gunzip -c /home/neale/Downloads/c3prd5.gz > /home/ibmsys1/zpdteduk/c3prd5
gunzip -c /home/neale/Downloads/c3dis1.gz > /home/ibmsys1/zpdteduk/c3dis1
gunzip -c /home/neale/Downloads/c3dis2.gz > /home/ibmsys1/zpdteduk/c3dis2
gunzip -c /home/neale/Downloads/c3dis3.gz > /home/ibmsys1/zpdteduk/c3dis3
gunzip -c /home/neale/Downloads/c3dbd1.gz > /home/ibmsys1/zpdteduk/c3dbd1
gunzip -c /home/neale/Downloads/c3dbd2.gz > /home/ibmsys1/zpdteduk/c3dbd2
gunzip -c /home/neale/Downloads/c3dbar.gz > /home/ibmsys1/zpdteduk/c3dbar
gunzip -c /home/neale/Downloads/c3c610.gz > /home/ibmsys1/zpdteduk/c3c610
gunzip -c /home/neale/Downloads/c3c620.gz > /home/ibmsys1/zpdteduk/c3c620
gunzip -c /home/neale/Downloads/c3imf1.gz > /home/ibmsys1/zpdteduk/c3imf1
gunzip -c /home/neale/Downloads/c3res2.gz > /home/ibmsys1/zpdteduk/c3res2
gunzip -c /home/neale/Downloads/c3usr1.gz > /home/ibmsys1/zpdteduk/c3usr1
gunzip -c /home/neale/Downloads/c3cfg1.gz > /home/ibmsys1/zpdteduk/c3cfg1
gunzip -c /home/neale/Downloads/c3uss1.gz > /home/ibmsys1/zpdteduk/c3uss1
gunzip -c /home/neale/Downloads/c3uss2.gz > /home/ibmsys1/zpdteduk/c3uss2
gunzip -c /home/neale/Downloads/c3uss3.gz > /home/ibmsys1/zpdteduk/c3uss3
gunzip -c /home/neale/Downloads/c3sys1.gz > /home/ibmsys1/zpdteduk/c3sys1
gunzip -c /home/neale/Downloads/c3w901.gz > /home/ibmsys1/zpdteduk/c3w901
gunzip -c /home/neale/Downloads/c3w902.gz > /home/ibmsys1/zpdteduk/c3w902
gunzip -c /home/neale/Downloads/c3blz1.gz > /home/ibmsys1/zpdteduk/c3blz1
gunzip -c /home/neale/Downloads/c3kan1.gz > /home/ibmsys1/zpdteduk/c3kan1
gunzip -c /home/neale/Downloads/c3inm1.gz > /home/ibmsys1/zpdteduk/c3inm1
gunzip -c /home/neale/Downloads/c3zcx1.gz > /home/ibmsys1/zpdteduk/c3zcx1
gunzip -c /home/neale/Downloads/c3zwe1.gz > /home/ibmsys1/zpdteduk/c3zwe1
```

So, I execute the unpack script and it runs for a while.
```
[root@localhost zpdteduk]# pwd
/home/ibmsys1/zpdteduk
[root@localhost zpdteduk]# cat unpack.sh 
gunzip -c /home/neale/Downloads/c3paga.gz > /home/ibmsys1/zpdteduk/c3paga
gunzip -c /home/neale/Downloads/c3pagb.gz > /home/ibmsys1/zpdteduk/c3pagb
gunzip -c /home/neale/Downloads/c3pagc.gz > /home/ibmsys1/zpdteduk/c3pagc
gunzip -c /home/neale/Downloads/c3prd1.gz > /home/ibmsys1/zpdteduk/c3prd1 
gunzip -c /home/neale/Downloads/c3prd2.gz > /home/ibmsys1/zpdteduk/c3prd2
gunzip -c /home/neale/Downloads/c3prd3.gz > /home/ibmsys1/zpdteduk/c3prd3
gunzip -c /home/neale/Downloads/c3prd4.gz > /home/ibmsys1/zpdteduk/c3prd4
gunzip -c /home/neale/Downloads/c3prd5.gz > /home/ibmsys1/zpdteduk/c3prd5
gunzip -c /home/neale/Downloads/c3dis1.gz > /home/ibmsys1/zpdteduk/c3dis1
gunzip -c /home/neale/Downloads/c3dis2.gz > /home/ibmsys1/zpdteduk/c3dis2
gunzip -c /home/neale/Downloads/c3dis3.gz > /home/ibmsys1/zpdteduk/c3dis3
gunzip -c /home/neale/Downloads/c3dbd1.gz > /home/ibmsys1/zpdteduk/c3dbd1
gunzip -c /home/neale/Downloads/c3dbd2.gz > /home/ibmsys1/zpdteduk/c3dbd2
gunzip -c /home/neale/Downloads/c3dbar.gz > /home/ibmsys1/zpdteduk/c3dbar
gunzip -c /home/neale/Downloads/c3c610.gz > /home/ibmsys1/zpdteduk/c3c610
gunzip -c /home/neale/Downloads/c3c620.gz > /home/ibmsys1/zpdteduk/c3c620
gunzip -c /home/neale/Downloads/c3imf1.gz > /home/ibmsys1/zpdteduk/c3imf1
gunzip -c /home/neale/Downloads/c3res2.gz > /home/ibmsys1/zpdteduk/c3res2
gunzip -c /home/neale/Downloads/c3usr1.gz > /home/ibmsys1/zpdteduk/c3usr1
gunzip -c /home/neale/Downloads/c3cfg1.gz > /home/ibmsys1/zpdteduk/c3cfg1
gunzip -c /home/neale/Downloads/c3uss1.gz > /home/ibmsys1/zpdteduk/c3uss1
gunzip -c /home/neale/Downloads/c3uss2.gz > /home/ibmsys1/zpdteduk/c3uss2
gunzip -c /home/neale/Downloads/c3uss3.gz > /home/ibmsys1/zpdteduk/c3uss3
gunzip -c /home/neale/Downloads/c3sys1.gz > /home/ibmsys1/zpdteduk/c3sys1
gunzip -c /home/neale/Downloads/c3w901.gz > /home/ibmsys1/zpdteduk/c3w901
gunzip -c /home/neale/Downloads/c3w902.gz > /home/ibmsys1/zpdteduk/c3w902
gunzip -c /home/neale/Downloads/c3blz1.gz > /home/ibmsys1/zpdteduk/c3blz1
gunzip -c /home/neale/Downloads/c3kan1.gz > /home/ibmsys1/zpdteduk/c3kan1
gunzip -c /home/neale/Downloads/c3inm1.gz > /home/ibmsys1/zpdteduk/c3inm1
gunzip -c /home/neale/Downloads/c3zcx1.gz > /home/ibmsys1/zpdteduk/c3zcx1
gunzip -c /home/neale/Downloads/c3zwe1.gz > /home/ibmsys1/zpdteduk/c3zwe1
[root@localhost zpdteduk]# ./unpack.sh 

```

The Resvols need to be unpacked using a special ZPDT program after the license key has been installed. The process goes like this.

```
/usr/z1090/bin/Z1090_ADCD_install /home/ibmsys1/c3res1.zPDT /home/ibmsys1/zpdteduk/c3res1 
```

## 1.3 Edit Device Map and IPL Script

Edit a device map file (z31c_devmap). The example below may need to be edited to suit your system. 

```
#ADCD z/OS 3.1 December 2023 basic definition: TCPIP QDIO definition 
[system]
memory 26G
3270port 3270
processors 4 cp cp cp cp 

#start tn3270 terminals - console / tso 
command 2 x3270 -model 4 localhost:3270 > /dev/null 2>&1 
command 2 x3270 -model 4 localhost:3270 > /dev/null 2>&1  

#IPL parm al JES2 warm and CICS, Db2, IMS - everything 
command 2 sync ipl a80 parm 0a82al

#IPL parm iz JES2 warm and z/OSMF 
#command 2 sync ipl a80 parm 0a82iz

#IPL parm cs JES2 cold start - initial 
#command 2 sync ipl a80 parm 0a82cs

[manager]
name aws3274 1 
device 0700 3279 3174 L700       
device 0701 3279 3174 L701           
device 0702 3279 3174 L702           
device 0703 3279 3174 L703           
device 0704 3279 3174 L704           
device 0705 3279 3174 L705           
device 0706 3279 3174 L706
device 0707 3279 3174 L707           
device 0708 3279 3174 L708           
device 0709 3279 3174 L709           
device 0710 3279 3174 L710      
  
[manager]
name awsckd 28  
device 0a80 3390 3990 /home/ibmsys1/zpdteduk/c3res1
device 0a81 3390 3990 /home/ibmsys1/zpdteduk/c3res2
device 0a82 3390 3990 /home/ibmsys1/zpdteduk/c3sys1
device 0a83 3390 3990 /home/ibmsys1/zpdteduk/c3cfg1
device 0a84 3390 3990 /home/ibmsys1/zpdteduk/c3uss1
device 0a85 3390 3990 /home/ibmsys1/zpdteduk/c3uss2
device 0a96 3390 3990 /home/ibmsys1/zpdteduk/c3uss3
device 0a86 3390 3990 /home/ibmsys1/zpdteduk/c3paga
device 0a87 3390 3990 /home/ibmsys1/zpdteduk/c3pagb
device 0a88 3390 3990 /home/ibmsys1/zpdteduk/c3pagc
device 0a89 3390 3990 /home/ibmsys1/zpdteduk/c3prd1
device 0a8a 3390 3990 /home/ibmsys1/zpdteduk/c3prd2
device 0a8b 3390 3990 /home/ibmsys1/zpdteduk/c3prd3
device 0a8c 3390 3990 /home/ibmsys1/zpdteduk/c3dis1
device 0a8d 3390 3990 /home/ibmsys1/zpdteduk/c3dis2
device 0a8e 3390 3990 /home/ibmsys1/zpdteduk/c3dis3
device 0a8f 3390 3990 /home/ibmsys1/zpdteduk/c3inm1
device 0a90 3390 3990 /home/ibmsys1/zpdteduk/c3c560
device 0a9a 3390 3990 /home/ibmsys1/zpdteduk/c3c610
device 0a97 3390 3990 /home/ibmsys1/zpdteduk/c3dbd1
device 0a98 3390 3990 /home/ibmsys1/zpdteduk/c3dbd2
device 0a94 3390 3990 /home/ibmsys1/zpdteduk/c3kan1 
device 0a9b 3390 3990 /home/ibmsys1/zpdteduk/c3usr1
device 0a9c 3390 3990 /home/ibmsys1/zpdteduk/c3w901
device 0aad 3390 3990 /home/ibmsys1/zpdteduk/c3w902
device 0a9e 3390 3990 /home/ibmsys1/zpdteduk/c3blz1
device 0a9f 3390 3990 /home/ibmsys1/zpdteduk/c3imf1
device 0aa1 3390 3990 /home/ibmsys1/zpdteduk/c3prd4
device 0a9d 3390 3990 /home/ibmsys1/zpdteduk/c3prd5
device 0aa2 3390 3990 /home/ibmsys1/zpdteduk/c3zcx1
device 0aa3 3390 3990 /home/ibmsys1/zpdteduk/c3dbar # a3DBAR should be on 0AA3, to match with IODF
device 0aae 3390 3990 /home/ibmsys1/zpdteduk/c3zwe1
device 0ab1 3390 3990 /home/ibmsys1/zpdteduk/sares1

[manager]
name awsosa 0024  --path=a0 --pathtype=OSD --tunnel_intf=y
device 400 osa osa --unitadd=0
device 401 osa osa --unitadd=1
device 402 osa osa --unitadd=2

[manager]
name awsosa 0022  --path=f0 --pathtype=OSD
device 404 osa osa
device 405 osa osa
device 406 osa osa

```

## 1.4 First IPL 

Execute the following command ( or edit it into a script like go.sh )

```
awsstart --map z31c_devmap

```

Watch the Linux Desktop. Some messages will appear in the terminal where you ran the IPL script, and a couple of 3270 sessions will appear. One of them will be the z/OS console.

![IPLSEQ01](/sessions/images/iplseq01.JPG)

On first IPL you will receive the message

![IPLSEQ02](/sessions/images/iplseq02.JPG)

## 1.5 Navigate ISPF/PDF Menus

To be added

## 1.6 Edit TCPIP stack in z/OS 

To be added

## 1.7 Test TCPIP connectivity 

To be added

## 1.8 Shutdown z/OS 

To be added

## Homework:
Practice IPL, TSO logon, TCPIP connectivity and Shutdown of z/OS 
