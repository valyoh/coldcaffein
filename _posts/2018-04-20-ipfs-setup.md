---
layout: post
title: How to install IPFS on Ubuntu 16.04
tags: [IPFS, Distributed File System]
---

InterPlanetary File System (IPFS) is a protocol and network designed to create a content-addressable, peer-to-peer method of storing and sharing hypermedia in a distributed file system. IPFS was initially designed by Juan Benet, and is now an open-source project developed with help from the community. For more information see https://en.wikipedia.org/wiki/InterPlanetary_File_System 

You can see a list of all supported platforms at this url https://dist.ipfs.io/#go-ipfs

* Download the required version from the IPFS distributions site.

```
wget https://dist.ipfs.io/go-ipfs/v0.4.14/go-ipfs_v0.4.14_linux-amd64.tar.gz
```

* Untar the archive and navigate in the directory. Then execute the install.sh script.

```
tar xzvf go-ipfs_v0.4.14_linux-amd64.tar.gz
cd go-ipfs
sudo ./install.sh
```
This script installs the binary in the /usr/local/bin folder.

* Test the installation. If successful you shoud see USAGE information and list of commnads.

```
ipfs help

USAGE
  ipfs - Global p2p merkle-dag filesystem.
  
    ipfs [--config=<config> | -c] [--debug=<debug> | -D] [--help=<help>] [-h=<h>] [--local=<local> | -L] [--api=<api>] <command> ...

SUBCOMMANDS
  BASIC COMMANDS
    init          Initialize ipfs local configuration
    add <path>    Add a file to IPFS
    cat <ref>     Show IPFS object data
    get <ref>     Download IPFS objects
    ls <ref>      List links from an object
    refs <ref>    List hashes of links from an object

```

* Initialize IPFS with this command

```
ipfs init
```
This set up a local reposiotry with a bunch of objects in it. It is located in $HOME/.ipfs

Thats all.
