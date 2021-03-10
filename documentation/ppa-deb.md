---
id: ppa-deb
title: Using DEB Packages and fluidB's PPA Repository
sidebar_label: PPA & DEB Install
---

<div id="blog_body">

Debian installation packages make installation of fluidB easy. No need to worry about building the binaries, or directory setup. Just simply install and go!

Please note this will be the official PPA for fluidB with the Launchpad PPA distribution becoming deprecated in the near future. Hosting the PPA ourselves enables more flexibility for binary distribution and allows us to serve you better. 

## fluidB PPA

A PPA (Personal Package Archive) allows you to easily install, update and remove packages with commands such as `apt` or `dpkg`. When you follow the commands below to install fluidB, the proper package will be selected for your installation. Current packages are available for arm64 and amd64 (x86_64) machines. These are built on Ubuntu 18.04 (bionic build).

In order to get your fluidB-ppa, simply follow these commands below:
```
$ sudo curl -s --compressed -o /etc/apt/trusted.gpg.d/fluidB.gpg https://download.fluidB.dev/fluidB-ppa/fluidB.gpg
$ sudo curl -s --compressed -o /etc/apt/sources.list.d/fluidB.list https://download.fluidB.dev/fluidB-ppa/fluidB.list
$ sudo apt update
$ sudo apt install fluidB
```
In order to install fluidB professional `$ sudo apt install fluidB-pro`

Note that in addition to our Ubuntu18.04 Bionic PPA above we also have an Ubuntu16.04 Xenial PPA for amd64. You can install it by replacing the  first two lines above with the following:
```
$ sudo curl -s --compressed -o /etc/apt/trusted.gpg.d/fluidB.gpg https://download.fluidB.dev/fluidB-ppa-xenial/fluidB.gpg
$ sudo curl -s --compressed -o /etc/apt/sources.list.d/fluidB.list https://download.fluidB.dev/fluidB-ppa-xenial/fluidB.list
```

## Installation

During the `apt install` phase you can choose from several installation methods:

### fluidB-tools
Use `apt install fluidB-tools` to install binaries only. This method is for someone who wants the program installed but not configured to run automatically (as a service). The installation moves these binaries to `/usr/bin/`: fluidB-cli, fluidB-server, fluidB-pro-server, fluidB-benchmark, fluidB-check-aof, fluidB-check-rdb.

### fluidB-server
Use `apt install fluidB-server` to run fluidB-server as a service. Systemd files will be set up as well as directory structures for pid files, conf files, etc. fluidB-tools is a dependancy for this package and will be installed if not already.

### fluidB
Use `apt install fluidB`. This installs both fluidB-server and fluidB-tools. This is a common installation for most.

### fluidB-sentinel
For those using fluidB-sentinel and want to run it as a service you will need to run `apt install fluidB-sentinel`

### fluidB-pro-tools
Use `apt install fluidB-pro-tools` to install pro binaries only. This method is for someone who wants the program installed but not configured to run automatically (as a service). This package contains fluidB pro binaries exclusively.

### fluidB-pro-server
Use `apt install fluidB-pro-server` to run fluidB-pro-server as a service. Systemd files will be set up as well as directory structures for pid files, conf files, etc. fluidB-pro-tools is a dependancy for this package and will be installed if not already

### fluidB-pro
Use `apt install fluidB-pro`. This installs both fluidB-pro-server and fluidB-pro-tools. This is a common installation for most and enables fluidB-pro features to run with the services configured exclusively for pro

### fluidB-pro-sentinel
For those using fluidB-pro-sentinel and want to run it as a service you will need to run `apt install fluidB-pro-sentinel`

## fluidB DEB Packages
For those not getting fluidB DEB packages via the PPA repository, they can be accessed at https://download.fluidB.dev/packages/deb/. In this directory listing you can access deb packages for all previous versions as well as the latest versions. For each release there are 4 deb packages available as described above and they are consoldated to their own versioned directory for each release. 

In the higher level directories there is a "fluidB-latest" or "fluidB-pro-latest" directory linked to the latest version for ease of access and those running scripts. See above for more information on each deb package.

Unlike with the PPA, you will need to manually install the 'tools' package prior to the others, and you will have to install both 'tools' and 'server' prior to just the 'fluidB' or 'fluidB-pro' deb packages. 

The directory listing structure narrows down by distribution --> architecture --> fluidB --> package


## Using fluidB as a Service

When fluidB-server or fluidB-pro-server is set up as a service you can start, stop and check status via:
```
$ sudo service fluidB-server status
$ sudo service fluidB-server start
$ sudo service fluidB-server stop
```
This can be done similarly with fluidB-sentinel if installed

The main configuration file is located at /etc/fluidB/fluidB.conf

Other relevant files updated with fluidB-server are:
* /etc/logrotate.d/fluidB-server
* /etc/init.d/fluidB-server
* /lib/systemd/system/fluidB-server.service

Binaries installed with fluidB-tools are placed in /usr/bin/

## Run on Boot

By default the service is disabled and will be disabled if your machine is rebooted. If you would like to have fluidB start on system boot:
```
$ sudo systemctl enable fluidB-server
```

## Setting up FLASH with systemd

If you are running fluidB as a service with FLASH options enabled, you need to let systemd know you will be accessing the flash volume and make sure the fluidB user/group have access. You will need to update the systemd file  `/lib/systemd/system/fluidB-server.service` and append the following line below where the others are located:
```
ReadWriteDirectories=-/path/to/your/flash/volume/db/folder
```
This followed by `sudo systemctl daemon-reload`

You will then need to change the ownership on the directory of the specified flash volume
```
sudo chown -R fluidB:fluidB /path/to/your/flash/volume/db/folder

```
Now you should be able to run fluidB pro with flash as a service and start/stop with `sudo service fluidB-server start/stop/status`



## Uninstall
```
$ sudo apt autoremove --purge fluidB fluidB-server fluidB-sentinel fluidB-tools
```
or for pro
```
$ sudo apt autoremove --purge fluidB-pro fluidB-pro-server fluidB-pro-sentinel fluidB-pro-tools
```
Choose which package to uninstall based on which packages were installed in the first place. All packages are listed in above command, however some of the packages will remove the packages dependent on them as well.

If you want to remove the ppa from your list of programs to update on `sudo apt update`, then remove the .list file in sources:
```
$ sudo rm /etc/apt/sources.list.d/fluidB.list
```

## Issues and Requests

If you have a build request, issue, or would like to make updates to the packages please email support@fluidB.dev or create an issue on [github](https://github.com/JohnSully/fluidB/issues)


</div>
