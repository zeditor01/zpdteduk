# Install ZPDT Driver

1. Download the ZPDT Driver
2. Install the Linux Pre-Requisites for ZPDT
3. Request a ZPDT License File
4. Try IPLing


## Redbook

There is a substantial redbook that describes installation and useage of ZPDT [here SG248205](https://www.redbooks.ibm.com/redbooks/pdfs/sg248205.pdf)

The notes is this page are a condensed list of deployment steps. The document above is far more comprehensive.

## 1. Download the ZPDT Driver

 
logon the [IBM resourcelink](https://www.ibm.com/support/resourcelink/) with your IBM id to access the ZPDT drivers.

Look down to wards the bottom of the page to find 1090 Support.

![rl02](/sessions/images/rl02.JPG)

The 1090 Support Page allows you to request the 1090 drivers ( which is what you need to install on your NUC ).

It also contains links to license files that you have previously requested.

![rl03](/sessions/images/rl03.JPG)


Click on the Drivers link, and download the latest Linux 64bit driver 

![rl04](/sessions/images/rl04.JPG)

## 2. Install the Linux Pre-Requisites for ZPDT


### Pre-Requisite Software

You will need the x3270 package. the z/OS master console will be accessed using x3270. 

Some other pre-requisites must also be installed.

Open a linux terminal and install these packages. (you may need to prefix the commands with ```sudo``` to get root privilege.

```
# yum install libstdc++.i686 
# yum install libnsl
# yum install perl
# yum install x3270 (Optional. A 3270 emulator package.)
# yum install x3270-x11 (Needed by x3270.)
```

### ZPDT User

Next - we need to create the ibmsys1 userid, which will be the owner of the ZPDT instance.
It needs to be in a group, and you need to set a password. the commands below will do the trick

```
sudo groupadd -g 970 zpdt
sudo useradd -u 1070 -g zpdt -m -d /home/ibmsys1 ibmsys1
passwd ibmsys1
usermod -aG wheel ibmsys1
```

### kernel settings

ZPDT requires some kernal setting to be correct. Edit ```/etc/sysctl.conf``` using nano or some other editor, with the following values.

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

Then activate the new kernel settings
```
# /sbin/sysctl -p /etc/sysctl.conf
```


### bashrc

Next, edit the .bashrc file for the ibmsys1 user that owns ZPDT. set some path values.

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

### Finally you can test whether the system meets the pre-requisites for ZPDT.

open a terminal as root, change directory to /usr/z1090/bin, and execute z1090instcheck

![z1090check](/sessions/images/z1090check.JPG)

Oops - my system fails on one check, but it still works.


## 3. Request a ZPDT License File

this involves 4 steps
1. generate a request file
2. upload the request file to resourcelink
3. download the granted license file
4. install the granted license file


### Step 1. generate a request file

Logon to linux, switch to the /usr/z1090/bin directory, and execute the request_license command

```
[ibmsys1@RHEL ~]$ cd /usr/z1090/bin
[ibmsys1@RHEL bin]$ sudo ./request_license
```

### Step 2. upload the request file to resourcelink

Using the firefox browser in your Linux PC, logon the [IBM resourcelink](https://www.ibm.com/support/resourcelink/) with your IBM id.

Choose lease extension, fill in the details, and upload your request file

![lease](/sessions/images/lease.JPG)

You must enter the serial number of your own ZPDT. the maximum date extension is 1 year.


### Step 3. download the granted license file

The request process says to wait an hour, but it usually works immediately.

Download the license file.


### step 4. install the granted license file

Open a terminal, switch to /usr/z1090/bin and execute the update license command.

```
sudo ./update_license ~/zpdt7859019774907418906rhel_1719912758-update.zip
```

## 4. Try IPLing








