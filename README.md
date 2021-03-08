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

 
  ### Distributed Multimodel real-time in memory database management system



  <br>

 <!-- TABLE OF CONTENTS -->
<details open="open">
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
       </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#preparations">Preparations</a></li>
        <li><a href="#building">Building</a></li>
        <li><a href="#Installation">Installation</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
    <li><a href="#gratitudes">Gratitudes</a></li>
    <li><a href="#requirements">Requirements</a></li>
    <li><a href="#cases">Cases</a></li>
    <li><a href="#roadmap">Roadmap</a></li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#faq">FAQ</a></li>
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


<!-- System Requirements -->
## Requirements

* Hardware: Intel or AMD
* RAM: 256 MB (minimal) or above
* Nodes: 2 (for very small cluster)
* Operating System: UNIX-like only (Linux, BSD, OpenIndiana) **Windows isn't supported
<br/><br/>

<!-- GETTING STARTED -->

<br>

## Cases

* Logs ageregation
* Caching system
* Queue server
* Pattern "Central dispatcher" (for sharding)
* Solution that sits in front of multiple data sources and allow them to be treated as a single json database

<br>

## Getting Started

Multithreading Architecture
---------------------------

fluidB works by running the normal Redis event loop on multiple threads.  Network IO, and query parsing are done concurrently.  Each connection is assigned a thread on accept().  Access to the core hash table is guarded by spinlock.  Because the hashtable access is extremely fast this lock has low contention.  Transactions hold the lock for the duration of the EXEC command.  Modules work in concert with the GIL which is only acquired when all server threads are paused.  This maintains the atomicity guarantees modules expect.

Unlike most databases the core data structure is the fastest part of the system.  Most of the query time comes from parsing the REPL protocol and copying data to/from the network.

Future work:
 - Allow rebalancing of connections to different threads after the connection
 - Allow multiple readers access to the hashtable concurrently

### Preparations

1. Install gcc, g++ and all dependencies

  ```sh
 sudo apt install build-essential nasm autotools-dev autoconf libjemalloc-dev tcl tcl-dev uuid-dev libcurl4-openssl-dev
  ```

### Building

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
 
 ### Installation
 
 You can install fluidB download the binary from our site https://fluidb.icu/#download
 
 1. Open the link above
 2. Click to "Download" button
 3. Copy the tar-archive in your home directory
 4. Open you terminal and then print ($-terminal prompt) `$ tar xvjf archive.tar.bz2`
 5. `$ cd fluidb && src`
 6. `$ ./fluidb-serv`
 
 
 
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


<!-- FAQ -->
## FAQ

#### This page provides answers to frequently asked questions regarding fluidB.

* **What is the main idea of your project?**
The key idea of our product is that it solves the problems of processing, storing and analyzing a large amount of continuously incoming data, mainly geolocation data. It is intended primarily for application software developers and DBAs (Database Administrators). Our main goal is to provide the user with a simple yet flexible and functional tool for working with data.


* **How is your project different from KeyDB?**

When developing our product, we took KeyDB as a basis, as an improved version of the Redis sub-database, easy and understandable to learn and operate. The difference between fluidB and KeyDB lies in the implementation of a shell module that allows you to run external modules (programs) that extend the basic functionality of the application without explicitly specifying the path to the module in the configuration file.


* **What is the multi-model database management system?**
A multi-model subdivision is such a subdivision that allows you to store data using different data storage schemes. For example, along with relational data (stored in the form of tables), data can be stored in the form of graphs, an associative array, etc. Thus, incredible flexibility in storing data is achieved, depending on the type of data, the most suitable tool for storing and processing it is used.


* **Is there a FluidB implementation for the Windows operating system?**
No, there is no implementation of FluidB for the Windows operating system. our application is client-server, with on-board caching subdivision, focused on use mainly on servers, and the vast majority of servers run under the Linux operating system. We do not plan to release a version for Windows in the future.


* **Is FluidB in Linux repositories?**
No, FluidB is still distributed as source and run-ready binaries on GitHub.

* **Is there a docker image of the project?**

No, there is no docker image, because docker, like any virtualization environment, forms an additional layer of abstraction, which complicates both the development process itself and the program operation process. Our goal: "To create a high-performance subassembly that is as easy to use as possible"


* **Why are you confident that you will be able to create a high-quality, competitive software product that people need?**

For several reasons. First, because our product is based on the well-proven Redis DBMS. Secondly, our software product is already used as a backend in the ws-stickleback project "Biology and Soil" of the Faculty of the Department of Ichthyology and Hydrobiology, St. Petersburg State University


* **Is there a paid version of your software product with advanced functionality?**

There is such a version, it is calledEnterprise-version, this version includes both the functionality required by the client (discussed individually with each client) and the default functionality.


* **How is authorization used?**

In the traditional sense, there is no authorization. We have a funny login form that we wrote to show how you can quickly implement it using JavaScript, it is used by default in our fluwc web interface, where you can make "typical requests" in the browser. FluidB is not intended to be publicly displayed. This is an application that works on the Internet, so we do not have authorization at this stage.


* **How does your product name stand for?**

The name of our project is deciphered as follows: FluidB, this is a reference to the English word "fluid", which means "fluid" - a physical term meaning "a state of matter with parameters above critical", which very accurately characterizes our product. A critical state of a substance means a state in which it disappears the difference between its liquid and vapor phases.
This is illustrated by the colors of the logo: At first, there is no load on the application (blue Greek letter "Phi"), then it increases sharply (three red subsequent letters), then the load begins to decrease and evenly distribute- orange symbol, differential, showing the rate of change of the value of the variable during balancing, and finally it is evenly distributed and becomes normal (seventh blue symbol). In our case, it should be interpreted as follows: thanks to our product, the line between low loads and very high loads is erased, i.e. to. it helps balance the load. A couple of words should be said separately about the symbol of the "flower" standing in front of the inscription: It personifies the mathematical graph of objects, tk. our subd is a multi-model supporting graph data storage model.

* **I have decided to purchase the Enterprise version of your product, where should I contact?**

You shoud write the letter in email: gvsafronov@gmail.com



<!-- CONTACT -->
## Contact

Grigoriy Safronov - gvsafronov@gmail.com

Project Link: [https://fluidb.icu](https://fluidb.icu)



