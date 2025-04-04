# Installing Linux

1. Download CentOS Stream 9 (Neale's choice) or RHEL 9 (Ben's choice)
2. Create Installable Media for Linux and run installation.
3. Install Linux
4. Customise Linux to meet your needs.


## 1. CentOS Stream 9 code

Download CentOS Stream 9 from [here](https://www.centos.org/)

Click on the 'Download" Tab on the top of the page.

Don't be tempted by CentOS Stream 10 just yet. It takes a while for many linux packages to get ported to each new version of centOS Stream. CentOS Stream 9 has everything you need to run ZPDT.

Download the x86_64 ISO package from one of the mirrors.

![centos9](/sessions/images/centos9.JPG)


## 2. Create Installable Media on a USB drive

I use a utility called Rufus to burn ISO images to a USB stick

[Download Rufus here](https://rufus.ie/en/)

1. install Rufus onto a Windows PC
2. insert an 8GB USB stick into your computer (it will be formatted)
3. Fill in the Rufus screen to burn CentOS from the ISO file onto the USB stick,

## 3. Install Linux

Insert the USB stick into your NUC or Laptop.

If using a NUC, you will need to connect a keyboard, mouse and screen to the NUC for installation and customisation.

Power on the System

Follow the installation prompts to accept a default install for Linux.

* Choose the Location / Timezone / Language
* Set a password for the root user that you can remember
* define another user (eg: neale) and a password
* Accept the default formatting of your 2TB SSD

Once the installation is finished, reboot and check that the Linux OS is working by logging on as (eg) neale.

## 4. Customise Linux to meet your needs.

Now that Linux is installed, you will want some basic linux packages to work with. 

There base utilities are sepearate from the pre-requisites that ZPDT will need. They are addressed in the next section.


### Statis IP Address

Your Linux NUC should run at a static IP address. this can be achieved by configuring Linux TCPIP to use a static address, or using address reservation on your router to allocate a particular TCPIP address for the box.

I use address reservation. Screenshot below : the bottom entry is my NUC (whch I call BEAZT).

![router](/sessions/images/router.JPG)


### XRDP.

I leave my NUC running 24*7 without a keyboard, mouse and screen. I use Windows Remote Desktop to connect into my NUC, in order to IPL and Shutdown zOS. Base Linux does not support Windows RDP, so you need to install and configure it.

Open a terminal on your Linux System, and 

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

```
# yum install libstdc++.i686 
# yum install libnsl
# yum install perl
# yum install x3270 (Optional. A 3270 emulator package.)
# yum install x3270-x11 (Needed by x3270.)
```

## ZPDT User
```
sudo groupadd -g 970 zpdt
sudo useradd -u 1070 -g zpdt -m -d /home/ibmsys1 ibmsys1
passwd ibmsys1
usermod -aG wheel ibmsys1
```

## kernel settings
```
# nano /etc/sysctl.conf (The following lines should begin in column 1.)
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

# /sbin/sysctl -p /etc/sysctl.conf
```

## bashrc
```
nano /home/ibmsys1/.bashrc 

export PATH=/usr/z1090/bin:$PATH
export LD_LIBRARY_PATH=/usr/z1090/bin:$LD_LIBRARY_PATH
export MANPATH=/usr/z1090/man:$MANPATH
ulimit -c unlimited
ulimit -d unlimited
ulimit -m unlimited (If more than 128 emulated I/O devices)
ulimit -v unlimited (If more than 128 emulated I/O devices)
```

## Further Edits
```
kernel.shmmax=36000000000
kernel.shmall=24000000
to allow larger memory to be exploited.
Still only using 14GB real memory.
awsstart wont let me run with Devmap memory over 36GB
Needs further research
```

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





