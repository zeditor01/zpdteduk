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


### Configure a Static IP Address

Your Linux NUC should run at a static IP address. this can be achieved by configuring Linux TCPIP to use a static address, or using address reservation on your router to allocate a particular TCPIP address for the box.

I use address reservation. Screenshot below : the bottom entry is my NUC (whch I call BEAZT).

![router](/sessions/images/router.JPG)


### Install and configure XRDP

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

At this point i would shutdown and restart linux, before attempting to open an RDP session to my NUC.

Once Linux is booted, from my Windows client I open the "Remote Desktop" application and connect to 192.168.1.170 

If you run a Mac - Mac app store has a free RDP app from Microsoft.

![rdp01](/sessions/images/rdp01.JPG)

then I can access my Linux Desktop from Windows client, to perform IPLs and shutdowns.

![rdp02](/sessions/images/rdp02.JPG)


