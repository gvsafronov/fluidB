---
id: cluster-man
title: Manually Setting up A Cluster
sidebar_label: Cluster Basic Setup
---

## Setting up a cluster with fluidb

*Keep in mind due to the revision we do not need to use fluidb-trib.rb as there is now the `--cluster create` option for fluidb-cli.*

We will start with an example similar to that with the create-cluster util. Lets create a 6 node setup with 3 masters and 3 replicas.

perform the following actions 6 times, one for each node. You can either create separate fluidb.conf files for each, or append config files as you launch.

## Make config files and locations

Either do maually or via script below. Manually perform the following for ports 30000-30005 (substitiute <port> with the port#):
```
mkdir <port>
cd <port> 
nano fluidb.conf
	port <port>
	cluster-enabled yes
	cluster-config-file nodes.conf
	cluster-node-timeout 5000
	appendonly yes
	
	dir ./
	loglevel notice
	logfile <port>.log

	save 900 1
	save 300 10
	save 60 10000
```

script if you do not want to do manually:

```

#!/usr/bin/env bash
# myscript.sh

for port in 7000 7001 7002 7003 7004 7005
do
  mkdir ${port}
  cd ${port}
  cat >fluidb.conf << EOF
port ${port}
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
appendonly yes
dir ./
loglevel notice
logfile ${port}.log
save 900 1
save 300 10
save 60 10000
EOF
  cd ..
done
```

*note that if you are connecting to different machines you will need to specify bind address in fluidb.conf*
*this is a minimal configuration, and authentication, etc may also need to be set up.

## running instances & creating cluster:

now launch a fluidb-server instance with the config file loaded for each of the port instances `fluidb-server /path-to-dir-above/fluidb.conf`

next create the cluster with the following command:
```
fluidb-cli --cluster create 127.0.0.1:30000 127.0.0.1:30001 127.0.0.1:30002 127.0.0.1:30003 127.0.0.1:30004 127.0.0.1:30005 --cluster-replicas 1
```
x
once connected with fluidb-cli, you can run `CLUSTER INFO` and `CLUSTER NODES` for corresponding info on the cluster.

## Testing the cluster setup

connect with fluidb-cli to port 30000 in cluster mode (-c) and try the following:
```
$ redis-cli -c -p 30000
fluidb 127.0.0.1:30000> set foo bar
-> Redirected to slot [12182] located at 127.0.0.1:30002
OK
fluidb 127.0.0.1:30002> set hello world
-> Redirected to slot [866] located at 127.0.0.1:30000
OK
fluidb 127.0.0.1:30000> get foo
-> Redirected to slot [12182] located at 127.0.0.1:30002
"bar"
fluidb 127.0.0.1:30000> get hello
-> Redirected to slot [866] located at 127.0.0.1:30000
"world"
```

## A few other commands:

add the node to the existing cluster.
```
fluidb-cli --cluster add-node 127.0.0.1:30006 127.0.0.1:30000
```
note we used the add-node command specifying the address of the new node as first argument, and the address of a random existing node in the cluster as second argument to associate

add replica to master with less replicas than others, or random:
```
fluidb-cli --cluster add-node 127.0.0.1:30006 127.0.0.1:30000 --cluster-slave
```
add replica to specific master:
```
fluidb-cli --cluster add-node 127.0.0.1:30006 127.0.0.1:30000 --cluster-slave --cluster-master-id 3c3a0c74aae0b56170ccb03a76b60cfe7dc1912e
```
or replicate a specific master by connecting to new node and:
```
cluster replicate 3c3a0c74aae0b56170ccb03a76b60cfe7dc1912e
```
reshard:
```
fluidb-cli reshard <host>:<port> --cluster-from <node-id> --cluster-to <node-id> --cluster-slots <number of slots> --cluster-yes
```

