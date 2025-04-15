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


## 1.3 Edit Device Map and IPL Script


## 1.4 First IPL 


## 1.5 Navigate ISPF/PDF Menus


## 1.6 Edit TCPIP stack in z/OS 


## 1.7 Test TCPIP connectivity 


## 1.8 Shutdown z/OS 


## Homework:
Practice IPL, TSO logon, TCPIP connectivity and Shutdown of z/OS 
