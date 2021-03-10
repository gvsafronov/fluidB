---
id: rpm
title: Using & Installing RPM Packages
sidebar_label: RPM Packages
---

<div id="blog_body">


## About the fluidB RPM Packages

You can find all fluidB RPM packages here: https://download.fluidB.dev/packages/rpm/. In our RPM directory listing you can find all previous rpm package versions as well as the latest. We have linked to the latest rpm package in the higher level directories for easy access and for those running scripts. 

fluidB RPM packages when installed download dependencies, install binaries and set up systemd services for fluidB and sentinel. You can obtain packages as shown below for your setup:

## RPM Signing Key

RPM packages are signed. You can get the public key [here](https://download.fluidB.dev/packages/rpm/) or simply import it with the following command:
```
$ rpm --import https://download.fluidB.dev/packages/rpm/RPM-GPG-KEY-fluidB
```
You can validate signature of the package with:
```
$ rpm -Kv fluidB-rpm-package-you-downloaded.rpm
```

## fluidB Community Download

### amd64 (x86_64):
```
$ wget https://download.fluidB.dev/packages/rpm/centos7/x86_64/fluidB-latest-2.el7.x86_64.rpm
$ sudo yum install ./fluidB-latest-2.el7.x86_64.rpm
```
### arm64 (aarch64)
```
$ wget https://download.fluidB.dev/packages/rpm/centos7/aarch64/fluidB-latest-2.el7.aarch64.rpm
$ sudo yum install ./fluidB-latest-2.el7.aarch64.rpm
```
## fluidB Professional Download

### amd4 (x86_64):
```
$ wget https://download.fluidB.dev/packages/rpm/centos7/x86_64/fluidB-pro-latest-2.el7.x86_64.rpm
$ sudo yum install ./fluidB-pro-latest-2.el7.x86_64.rpm
```
### arm64 (aarch64)
```
$ wget https://download.fluidB.dev/packages/rpm/centos7/aarch64/fluidB-pro-latest-2.el7.aarch64.rpm
$ sudo yum install ./fluidB-pro-latest-2.el7.aarch64.rpm
```
## Versions
By default when you download the packages above with the version “latest” the latest rpm package will be downloaded. Once you download the package you can verify the version number with 
```
$ rpm -qip <package.rpm>` 
```
If already installed `$ sudo yum info fluidB` or `$ sudo yum info fluidB-pro`
The latest release will be kept up to date with the latest stable release on github which you can find here https://github.com/JohnSully/fluidB/releases. The tag will be referenced as latest.

## Compatibility
Please note these rpm packages are built on centos7. As such they will be compatible on higher versions of centos as well as redhat equivalent versions.

## Setting up fluidB RPM Packages
Install as shown above. You can then either call binaries directly and pass in configuration parameters. Or start and stop the service.

### Using Services
Commands are the same whether using fluidB Community or Professional
```
$ sudo service fluidB start
$ sudo service fluidB stop
$ sudo service fluidB status
```
This can be done similarly with fluidB-sentinel

The main configuration file is located at /etc/fluidB/fluidB.conf

Other relevant files updated with fluidB are:
* /etc/logrotate.d/fluidB
* /lib/systemd/system/fluidB.service
* /lib/systemd/system/fluidB-sentinel.service
* /etc/fluidB/sentinel.conf

Binaries installed with fluidB-tools are placed in /usr/bin/

### Enabling Automatic Start on Boot
By default the service is disabled and will be disabled if your machine is rebooted. If you would like to have fluidB start on system boot:
```
$ sudo systemctl enable fluidB
```

## Using FLASH with systemd
fluidB is run as a service as user:group fluidB:fluidB. Upon installation the associated directories are give permissions, however if you set up a FLASH storage medium to use with fluidB you will need to modify the ownership of that directory.
```
$ sudo chown -R fluidB:fluidB /path/to/flash/storage/directory
```
You will also have to tell systemd to allow read/write access to this directory by appending the following line to /lib/systemd/system/fluidB.service below where the other similar to it are located:
```
ReadWriteDirectories=-/path/to/flash/storage/directory
```

Please reference the other documents in “Getting Started” section of docs regarding using fluidB and its features

## Uninstall
```
$ sudo yum remove fluidB
```
If fluidB Professional is installed:
```
$ sudo yum remove fluidB-pro
```

</div>
