<!--
*** Thanks for checking out the Best-README-Template. If you have a suggestion
*** that would make this better, please fork the repo and create a pull request
*** or simply open an issue with the tag "enhancement".
*** Thanks again! Now go create something AMAZING! :D
-->



<!-- PROJECT LOGO -->
<p align="center">
  <a href="https://fluidb.icu"><img src="/img/logo.png" alt="fluidB"></a>
</p>
<p align="center">
  
  <div class="myWrapper" markdown="1">
  
  ### Distributed Multimodel real-time in memory database management system

</div>

  <br>

 <!-- TABLE OF CONTENTS -->
<details open="open">
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
    <li><a href="#gratitudes">Gratitudes</a></li>
    <li><a href="#roadmap">Roadmap</a></li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
    </ol>
</details>



<!-- ABOUT THE PROJECT -->
## About The Project

Fluidb is an open source (BSD-3-Clause License), in-memory database management system, distributed uder BSD-3-Clause License using code of KeyDB, which also distributed uder BSD-3-Clause License. The purpose of our project is to fix fundamental flaws in Redis, such as scaling, creating a multi-threaded server.

We do not have a docker image since docker, like any virtualization environment, forms an additional layer of abstraction that complicates both the development process itself and the program operation process. Our goal: "To create a high-performance subassembly that is as easy to use as possible"

fluidB is often referred to as a data structures server. What this means is that fluidB provides access to mutable data structures via a set of commands, which are sent using a server-client model with TCP sockets and a simple protocol. So different processes can query and modify the same data structures in a shared way. The storage of fluidB is implemented as follows: data can be stored according to the "key-value" model, or can be stored as a graph, which is a chain of interrelated events (which are similar to frames from an old film strip), which together represent a description of some event.

Good example is to think of fluidB as a more complex version of memcached, where the operations are not just SETs and GETs, but operations that work with complex data types like Lists, Sets, ordered data structures, and so forth.


## Gratitudes

* **Salvatore Sanfilippo (antirez)**, I would like to express our gratitude for all that you have done!!! Thank you.
* **John Sully**, **Ben Shermel** I wish to express my appreciation for all your efforts!!!
<br/><br/>


<!-- GETTING STARTED -->
## Getting Started

Multithreading Architecture
---------------------------

fluidB works by running the normal Redis event loop on multiple threads.  Network IO, and query parsing are done concurrently.  Each connection is assigned a thread on accept().  Access to the core hash table is guarded by spinlock.  Because the hashtable access is extremely fast this lock has low contention.  Transactions hold the lock for the duration of the EXEC command.  Modules work in concert with the GIL which is only acquired when all server threads are paused.  This maintains the atomicity guarantees modules expect.

Unlike most databases the core data structure is the fastest part of the system.  Most of the query time comes from parsing the REPL protocol and copying data to/from the network.

Future work:
 - Allow rebalancing of connections to different threads after the connection
 - Allow multiple readers access to the hashtable concurrently

### Prerequisites

1. Install gcc, g++ and all dependencies

  ```sh
 sudo apt install build-essential nasm autotools-dev autoconf libjemalloc-dev tcl tcl-dev uuid-dev libcurl4-openssl-dev
  ```

### Installation

1. Install gcc, g++ and all dependencies (see Prerequisites)
2. Clone the repo
   ```sh
   git clone https://github.com/gvsafronov/fluidb.git
   ```
3. Build project with make
   ```sh
   cd fluidb && make
   ```
4. To build with TLS support, you'll need OpenSSL development libraries (e.g. libssl-dev on Debian/Ubuntu) and run:`
   ```
   make BUILD_TLS=yes';
   ```
   
### Running fluidB


To run fluidB with the default configuration just type:

    $ cd src
    $ ./fluidB-serv

 

If you want to provide your fluidB.conf, you have to run it using an additional
parameter (the path of the configuration file):

    $ ./fluidB-serv --port 9999 --replicaof 127.0.0.1 6379
    $ ./fluidB-serv /etc/fluidB/6379.conf --loglevel debug

All the options in fluidB.conf are also supported as options using the command
line, with exactly the same name.

It is possible to alter the fluidB configuration by passing parameters directly
as options using the command line. Examples:

    % ./fluidB-serv --port 9999 --replicaof 127.0.0.1 6379
    % ./fluidB-serv /etc/fluidB/6379.conf --loglevel debug

All the options in fluidB.conf are also supported as options using the command
line, with exactly the same name.




<!-- USAGE EXAMPLES -->
## Usage

_For useful examples of how a project can be used, please refer to the [Documentation](https://docs.keydb.dev/docs/intro/)_

### Playing with fluidB


You can use fluidB-cli to play with fluidB. Start a fluidB-server instance,
then in another terminal try the following:

    % cd src
    % ./clif
    fluidB> ping
    PONG
    fluidB:~> set foo bar
    OK
    fluidB:~> get foo
    "bar"
    fluidB:~> incr mycounter
    (integer) 1
    fluidB:~> incr mycounter
    (integer) 2
    fluidB:~>

You can find the list of all the available commands at https://docs.fluidB.dev/docs/commands/


Fixing build problems with dependencies or cached build options
---------

fluidB has some dependencies which are included into the `deps` directory.
`make` does not automatically rebuild dependencies even if something in
the source code of dependencies changes.

When you update the source code with `git pull` or when code inside the
dependencies tree is modified in any other way, make sure to use the following
command in order to really clean everything and rebuild from scratch:

    make distclean

This will clean: jemalloc, lua, hiredis, linenoise.

Also if you force certain build options like 32bit target, no C compiler
optimizations (for debugging purposes), and other similar build time options,
those options are cached indefinitely until you issue a `make distclean`
command.


Allocator
---------

Selecting a non-default memory allocator when building fluidB is done by setting
the `MALLOC` environment variable. fluidB is compiled and linked against libc
malloc by default, with the exception of jemalloc being the default on Linux
systems. This default was picked because jemalloc has proven to have fewer
fragmentation problems than libc malloc.

To force compiling against libc malloc, use:

    % make MALLOC=libc

To compile against jemalloc on Mac OS X systems, use:

    % make MALLOC=jemalloc

Verbose build
-------------

fluidB will build with a user friendly colorized output by default.
If you want to see a more verbose output use the following:

    % make V=1



<!-- ROADMAP -->
## Roadmap

See the [open issues](https://github.com/gvsafronov/fluidb/issues) for a list of proposed features (and known issues).

## New Configuration Options

With new features comes new options:

    server-threads N
    server-thread-affinity [true/false]

The number of threads used to serve requests.  This should be related to the number of queues available in your network hardware, *not* the number of cores on your machine.  Because fluidB uses spinlocks to reduce latency; making this too high will reduce performance.  We recommend using 4 here.  By default this is set to one.

    scratch-file-path /path

If you would like to use the [FLASH backed](https://github.com/JohnSully/fluidB/wiki/FLASH-Storage) storage this option configures the directory for fluidB's temporary files.  This feature relies on snapshotting to work so must be used on a BTRFS filesystem.  ZFS may also work but is untested.  With this feature fluidB will use RAM as a cache and page to disk as necessary.  NOTE: This requires special compilation options, see Building fluidB below.
    
    db-s3-object /path/to/bucket

If you would like fluidB to dump and load directly to AWS S3 this option specifies the bucket.  Using this option with the traditional RDB options will result in fluidB backing up twice to both locations.  If both are specified fluidB will first attempt to load from the local dump file and if that fails load from S3.  This requires the AWS CLI tools to be installed and configured which are used under the hood to transfer the data.

    active-replica yes

If you are using active-active replication set `active-replica` option to “yes”. This will enable both instances to accept reads and writes while remaining synced. [Click here](https://docs.fluidB.dev/docs/active-rep/) to see more on active-rep in our docs section. 

All other configuration options behave as you'd expect.  Your existing configuration files should continue to work unchanged.



<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to be learn, inspire, and create. Any contributions you make are **greatly appreciated**.

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request



<!-- LICENSE -->
## License

Distributed under the MIT License. See `LICENSE` for more information.



<!-- CONTACT -->
## Contact

Grigoriy Safronov - gvsafronov@gmail.com

Project Link: [https://fluidb.icu](https://fluidb.icu)



