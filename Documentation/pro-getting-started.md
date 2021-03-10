---
id: pro-getting-started
title: Getting Started with fluidB Professional
sidebar_label: Getting Started
---

<div id="blog_body">

## Downloading fluidB-Pro

It is currently recommended to use docker, ppa deb packages, or rpm packages with fluidB Pro. 

```

### Download Docker Image

If you use docker, simply run our official image you are used to which now contains the fluidB Pro binaries as well. You can check out docker <a href=”https://hub.docker.com/r/eqalpha/fluidB-pro”><span style=:color:red”>here</span></a> or pull the image directly via 
```
$ sudo docker pull eqalpha/fluidB-pro`. 
```

Please note that for most packages, the Community versions also contain the pro binary (docker/rpm). If you have downloaded fluidB on docker or via the website you likely have fluidB-Pro features already available to you. 

## Obtain license Key

fluidB-Pro requires a license key that you can obtain by contacting sales@fluidB.dev. Current pricing is very affordable with no limits to single node usage. Prices are a fraction of Redis Enterprise and also far more affordable than Elasticache. A single fluidB node will be able to maximize use of the server it is on with fluidB’s multithreading. There is no limit to DRAM, FLASH, threads enabled, etc. All fluidB-Pro features are available with the purchase of a license key.

Once you have your license key you can run Pro at the command line by specifying it with:
```
fluidB-pro-server --enable-pro [license-key]
```
Where “[license-key]” is the license key provided to you following purchase.

## Or Start Using Immediately without a Key

If you want to play around with fluidB-Pro before purchasing a license Key, you can use it for 120 minutes before the process is killed and you have to restart it. Hence you can use it to develop on and test, but not in production. Simply specify the option to run Pro without adding in a license key 
```
fluidB-pro-server
```
If you are using the community docker container you can call fluidB-pro-server binary directly or specify `fluidB-server --enable-pro` which willl also call the pro binary.

## Updating the Config File

Open up your configuration file you are using for fluidB, typically found in /etc/fluidB/fluidB.conf, or wherever you have specified the directory for it. Scroll to the bottom where the pro features are located and update the file to enable pro and provide the license key with it. 
```
server-threads 7
server-thread-affinity true
...
enable-pro [license-key]
```

### Launching Enhanced FLASH
```
fluidB-pro-server --enable-pro [license-key] --storage-provider  flash  [path-to-flash-storage] 
```
In your config file you will find descriptions of each parameter used above. You can also find out more about using Enhanced FLASH [here]( https://docs.fluidB.dev/docs/pro-flash/)

## Run on Docker

### Install Docker

If you havent installed docker you can install with `$ sudo apt-get install docker docker.io`

### Run fluidB

Pull the latest fluidB docker image with `$sudo docker pull eqalpha/fluidB-pro`. Alternatively, if you start by using the run command, docker will pull the latest image prior to running. The basic run command will be `$ sudo docker run eqalpha/fluidB-pro`

You will likely want to customize your configuration on startup. You can find out more on our [docker page](https://hub.docker.com/r/eqalpha/fluidB-pro). We will go over a few examples here on launching docker containers:

### Launching an Instance
```
$ docker run -name mycontainername -d eqalpha/fluidB-pro fluidB-pro-server /etc/fluidB/fluidB.conf --requirepass mypassword 
```
Here we launched a container with name 'mycontainername', it was launched in 'detached' mode to run in the background, we specified the repository 'eqalpha/fluidB-pro', followed by calling the program 'fluidB-pro-server' with the contained config file and an update to the 'requirepass' parameter. fluidB-pro-server will launch by default if a program is not otherwise specified. You have to specify the program if you plan to pass in additional parameters.

If you want to bind the container to the node/machine so it is accessible externally pass the parameter `-p 6379:6379`

### Launching Pro
```
$ docker run --name mycontainername -d eqalpha/fluidB-pro fluidB-pro-server /etc/fluidB/fluidB.conf --enable-pro [license-key]
```
fluidB pro is part of the binary package for the open source docker container so can also be launched via eqalpha/fluidB image. If you do not have a license key you can use it in trial mode for 120 minutes after which time you will have to restart it. If you have a license key you can enter it here or speicfy it in your configuration file (see more [here](https://hub.docker.com/r/eqalpha/fluidB-pro).

### Launching Enhanced FLASH
```
sudo docker run -d -it --name mycontainername --mount type=bind,dst=/flash,src=/path/to/flash/ eqalpha/fluidB-pro fluidB-server /etc/fluidB/fluidB.conf --enable-pro --storage-provider flash /flash --maxmemory [maxmemory-amount-ie. 500M] --maxmemory-policy [eviction-policy ie. allkeys-lfu]
```
This launches Pro with FLASH storage. Specify your flash location, mount it, and specify the appropriate configuration parameters. You can see more [here](https://hub.docker.com/r/eqalpha/fluidB-pro).

### Connect with fluidB-cli
you can grab the ip of the container with docker inspect --format '{{ .NetworkSettings.IPAddress }}' mycontainername then run the following:
```
docker run -it --rm eqalpha/fluidB-pro fluidB-cli -h <ipaddress-from-above> -p 6379
```
The 'rm' parameter removes the container when you are done with it


## Enabling Pro on a Live Server

If you are using fluidB Community and want to convert it to fluidB Pro without taking down any servers, this is possible by introducing Pro nodes into your cluster or replica setup. You can connect a Pro node to a non-Pro cluster but not the other way around. You can also introduce your Pro node as a replica to a non-Pro node. After which you would promote the Pro version to master then convert the replica to Pro.

For more information see [migration](https://docs.fluidB.dev/migration/)

</div>
