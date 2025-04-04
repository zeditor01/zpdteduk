# Install ZPDT Driver

1. Download the ZPDT Driver
2. Install the Linux Pre-Requisites for ZPDT
3. Request a ZPDT License File
4. Install the License File and Try IPLing


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


3. Request a ZPDT License File


4. Install the License File and Try IPLing



## 8-core zPDT licenses in a token are usually valid for a year and must be renewed afterwards.

```
1. Insert the USB token.

2. Request an update file:

[ibmsys1@RHEL ~]$ cd /usr/z1090/bin
[ibmsys1@RHEL bin]$ sudo ./request_license
[ibmsys1@RHEL bin]$ sudo find / -name rhel_1719915108.zip
[ibmsys1@RHEL bin]$ sudo mv /root/rhel_1719915108.zip ~


3. Transfer ~/rhel_1719912758.zip to your PC in binary

4. Request a 1090 Lease Date Extension through IBM Resource Link: 1090 Date Extension Request and supply the following details to complete the request:
Machine Model
1090-LT8 (8 CPU)
1090 S/N
0221B4B

https://www.ibm.com/support/resourcelink/zpdt

5. Wait an hour for an email with the generated license file.  You can also check the status through IBM Resource Link: 1090 Tool - Secure Update Files

6. Transfer your zpdt7859019774907418906rhel_1719912758-update.zip from your PC

to ~/zpdt7859019774907418906rhel_1719912758-update.zip and install the license file.

sudo ./update_license ~/zpdt7859019774907418906rhel_1719912758-update.zip


7. Confirm the expiry date by using the token command.  This command requires awsstart to have been run:


[ibmsys1@localhost zpdt20240707]$ /usr/z1090/bin/token
CPU 0, zPDTA(1090) available and working. Serial=6987(0x1B4B) Lic=138059(0x21B4B)  EXP=7/7/2025 LDK-HL   
CPU 1, zPDTA(1090) available and working. Serial=6987(0x1B4B) Lic=138059(0x21B4B)  EXP=7/7/2025 LDK-HL   
CPU 2, zPDTA(1090) available and working. Serial=6987(0x1B4B) Lic=138059(0x21B4B)  EXP=7/7/2025 LDK-HL   
CPU 3, zPDTA(1090) available and working. Serial=6987(0x1B4B) Lic=138059(0x21B4B)  EXP=7/7/2025 LDK-HL   
CPU 4, zPDTA(1090) available and working. Serial=6987(0x1B4B) Lic=138059(0x21B4B)  EXP=7/7/2025 LDK-HL   
CPU 5, zPDTA(1090) available and working. Serial=6987(0x1B4B) Lic=138059(0x21B4B)  EXP=7/7/2025 LDK-HL   
CPU 6, zPDTA(1090) available and working. Serial=6987(0x1B4B) Lic=138059(0x21B4B)  EXP=7/7/2025 LDK-HL   
CPU 7, zPDTA(1090) available and working. Serial=6987(0x1B4B) Lic=138059(0x21B4B)  EXP=7/7/2025 LDK-HL   


 End of zPDTA Status display 
```

## Backups

### simple uncompressed backup in my 4TB Samsung 990PRO SSD ?

* copying 500GB of zvols files onto another path of the Samsung 990PRO is instantaneous ( and unbelievable ).
* Hence - I do not trust this backup mechanism.
* So, I choose to copy to USB disk ( device separation backup ) and then back to a backup folder on the Samsung 990 Pro

Insert a USB SSD disk - format to ext4 - mount at /run/media/neale/zbu

mkdir zbuyyyymmdd 

cd /run/media/neale/zbu/zbuyyyymmdd

cp /home/ibmsys1/zpdt2025/* .

Then copy back to a path in Beazt

### Create a Tarball and test it... 

Create tarball
```
cd /home/ibmsys1/zbu

tar -czvf large_archive.tar.gz /home/ibmsys1/zpdt2025

-c: Create a new archive
-z: Compress the archive using gzip
-v: Verbose mode (optional, but useful for seeing the progress)
-f: Specify the filename of the archive
```

Expand tarball

```
cd /home/ibmsys1/zpdtnewdir

tar -xzvf large_archive.tar.gz

-x: Extract files from the archive
-z: Decompress the archive using gzip
-v: Verbose mode (optional)
-f: Specify the filename of the archive to extract
```




