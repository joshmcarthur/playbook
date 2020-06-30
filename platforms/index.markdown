---
layout: default
title: Operations
nav_order: 90
---

Work in progress
{: .label .label-blue }

### First principles

The first step is to understand the environment that the application is running
in. Knowing this gives an idea of the kind of tools and processes that should be
applied when working on issues or features.

Key things to understand are:

* Is the environment temporary or permanent - is it a snapshot of an OS, a
  container, or a VM that will persist? This gives an idea both about the type
  of issues that could arise, as well as the degree to which the environment
  needs to be kept in a restorable state.
* What's the environment OS? Almost certainly Linux, but what flavour? Ubuntu
  and Debian are common, but Redhat or Fedora, or more specialised distros can
  be encountered as well. The environment OS will dictate how core packages are
  installed and managed. A good starting point is `uname -a` - this will tell
  you the kernel version and architecture. From here, Debian versions can be
  checked with `lsb_release -a`. Knowing the Debian or Ubuntu release that is
  being used is important when checking whether a particular version of a
  package is available.
* What resources are available? How pressured are they? CPU and memory are the
  main things to check, but disk capacity and network speed should also be
  known. There's a tonne of tools to introspect the resources available on a
  system. `top` and `free` are almost always installed and a quick way to check.
  For something a bit more powerful, `htop` is useful to have installed for a
  bit more detail and filtering/searching control. For disk information, `fdisk
  -l` will list installed disks (not just those mounted), and `df -h` and `df
  -i` will show available space and inodes respectively. Inodes as well as free
  space are important to keep track of - inodes are a bit like an address book -
  a disk can have free space, but if there are no free inodes, then data is
  still not persistable and the disk will still return an error about the device
  being full.
* How is the application run? Is it running inside a container? If so, is it run
  with Docker? Docker Compose? Kubernetes? Something else? Knowing _how_ the
  application is being run is a really important piece of context to be aware
  of. There may be a host system underlying the application container OS that
  also needs looking into.

### Heroku is great to reduce maintenance overhead

Coming soon
{: .label .label-yellow }

### AWS costs Real Money

Coming soon
{: .label .label-yellow }

### Installing Ruby

Coming soon
{: .label .label-yellow }

### 12-factor application 

Coming soon
{: .label .label-yellow }

### SSH public key authentication

Coming soon
{: .label .label-yellow }

### Security Updates

Coming soon
{: .label .label-yellow }

### Deployment

Coming soon
{: .label .label-yellow }

### User roles & permissions

Coming soon
{: .label .label-yellow }

### Application server state should be ephemeral

Coming soon
{: .label .label-yellow }

### Docker & Kubernetes

Coming soon
{: .label .label-yellow }