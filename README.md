![IncludeOS](./doc/IncludeOS_logo.png)
================================================
IncludeOS is a [Unikernel](https://en.wikipedia.org/wiki/Unikernel) written from scratch in C++, designed for x86 hardware virtualization, with no dependencies except for the virtual hardware. [Read more on the wiki](https://github.com/hioa-cs/IncludeOS/wiki). 

## It's a research prototype!
IncludeOS is not production ready, not feature complete, and very much a work in progress. However, it has been shown to outperform Linux virtual machines in terms of CPU usage by 5-20%, and memory usage by orders of magnitude, running a simple DNS service (both platforms ran the same binary). Preliminary performance results and a (now outdated) overview of IncludeOS appeared in an [IEEE CloudCom 2015](http://2015.cloudcom.org/) paper, titled *IncludeOS: A resource efficient unikernel for cloud services*. A [preprint is available here](doc/papers/IncludeOS_IEEE_CloudCom2015_PREPRINT.pdf), but for any [citations please refer to the publications section](https://github.com/hioa-cs/IncludeOS/wiki/Publications) in the [Wiki](https://github.com/hioa-cs/IncludeOS/wiki). 

IncludeOS is free software, with "no warranties or restrictions of any kind".

[![Early Prototype](https://img.shields.io/badge/IncludeOS-v0.7.0--proto-yellow.svg)](https://github.com/hioa-cs/IncludeOS/releases)
[![Apache v2.0](https://img.shields.io/badge/license-Apache%20v2.0-blue.svg)](http://www.apache.org/licenses/LICENSE-2.0)
[![Join the chat at https://gitter.im/hioa-cs/IncludeOS](https://badges.gitter.im/hioa-cs/IncludeOS.svg)](https://gitter.im/hioa-cs/IncludeOS?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

**Note:** *Anything may change at any time. The public API should not be considered stable.* This is the meaning of the 0 in the version number, as intended by [Semver](http://semver.org/).


## Build status
We're working towards automating everything with our Jenkins CI server. The tests performed to generate these badges are taken from the tests folder. More tests are added regularly, so to see which tests have been completed to generate the results click the corresponding badge. 

|        | Build from bundle                                                                                                                                             | Build from source |
|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------|
| Master | [![Build Status](https://jenkins.includeos.org/buildStatus/icon?job=shield_master_bundle_pipe)](https://jenkins.includeos.org/job/shield_master_bundle_pipe/) | Coming soon       |
| Dev    | [![Build Status](https://jenkins.includeos.org/buildStatus/icon?job=shield_dev_bundle_pipe)](https://jenkins.includeos.org/job/shield_dev_bundle_pipe/)       | Coming soon       |

### Key features
* **Extreme memory footprint**: A minimal bootable image, including bootloader, operating system components and a complete C++ standard library is currently 693K when optimized for size.
* **KVM and VirtualBox support** with full virtualization, using [x86 hardware virtualization](https://en.wikipedia.org/wiki/X86_virtualization) whenever available (it is on most modern x86 CPU's). In principle IncludeOS should run on any x86 hardware platform, even on a physical x86 computer, given appropriate drivers. Officially, we develop for- and test on [Linux KVM](http://www.linux-kvm.org/page/Main_Page), which power the [OpenStack IaaS cloud](https://www.openstack.org/), and [VirtualBox](https://www.virtualbox.org), which means that you can run your IncludeOS service on both Linux, Microsoft Windows and Apple OS X. 
* **C++11/14 support**
    * Full C++11/14 language support with [clang](http://clang.llvm.org) v3.8 and later.
    * Standard C++ library (STL) [libc++](http://libcxx.llvm.org) from [LLVM](http://llvm.org/)
    * Exceptions and stack unwinding (currently using [libgcc](https://gcc.gnu.org/onlinedocs/gccint/Libgcc.html))
    * *Note:* Certain language features, such as threads and filestreams are currently missing backend support. 
* **Standard C library** using [newlib](https://sourceware.org/newlib/) from [Red Hat](http://www.redhat.com/)
* **Virtio Network driver** with DMA. [Virtio](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=virtio) provides a highly efficient and widely supported I/O virtualization. Like most implementations IncludeOS currently uses "legacy mode", but we're working towards the new [Virtio 1.0 OASIS standard](http://docs.oasis-open.org/virtio/virtio/v1.0/virtio-v1.0.html)
* **A highly modular TCP/IP-stack** written from scratch, still under heavy development.
    * TCP: Not all the RFC's are impelemented yet, but it's stable and usable, including congestion control and common opitons.
    * UDP: More or less complete, enough to support a high performance DNS service
    * DHCP: Basic support, tested on VirtualBox, KVM and OpenStack
    * ICMP: Enough to answer ping, no integration with the rest of the stack yet.
    * ARP
    * Ethernet
    * IPv6 support under development

A longer list of features and limitations is on the [wiki feature list](https://github.com/hioa-cs/IncludeOS/wiki/Features)

# Try it out!

Here is a video showing how to set it up and get started with IncludeOS:

[![Getting started with IncludeOS video](http://img.youtube.com/vi/b2D6loApw3o/0.jpg)](http://www.youtube.com/watch?v=b2D6loApw3o)

## Building with Vagrant

You can use
[Vagrant](https://github.com/hioa-cs/IncludeOS/wiki/Vagrant) to set up
a virtual machine with the correct environment for building
IncludeOS. The following commands will build and install IncludeOS
into your home directory (`~/IncludeOS_install/`). The directory is mapped as a shared folder into the virtual machine vagrant creates.

```
     $ git clone https://github.com/hioa-cs/IncludeOS.git
     $ cd IncludeOS
     $ vagrant up
     $ vagrant ssh --command=/IncludeOS/etc/install_from_bundle.sh
```
You can now log in to the vagrant build environment and build and run a test service like so:

```
      $ vagrant ssh
      $ ./test.sh
```

## Prerequisites for building IncludeOS VM's
  * **Ubuntu 16.04 or 14.04 LTS, x86_64**, either on a physical or virtual machine (A virtualbox VM works fine)
     * For the full source build, you'll need at least 1024 MB memory
     * In order to support VGA graphics inside a VM, we recommend a lightweight GUI, such as  [lubuntu](https://help.ubuntu.com/community/Lubuntu/GetLubuntu) which runs great inside a virtual machine.
         * *NOTE:* Graphics is by no means necessary, as all IncludeOS output by default will be routed to the serial port, and in Qemu,
     * The install scripts may very well work on other flavours on Linux, but we haven't tried. Please let us know if you do.
     * **Building on a Mac:** you can build IncludeOS (from bundle only) directly on a Mac by running [./etc/install_osx.sh](./etc/install_osx.sh).
  * You'll need `git` to clone from github.

Once you have a system with the prereqs (virtual or not), you can choose a full build from source, or a fast build from binaries:

## A) Install libraries from binary bundle (fast)
    $ sudo apt-get install git
    $ git clone https://github.com/hioa-cs/IncludeOS
    $ cd IncludeOS
    $ ./etc/install_from_bundle.sh

**The script will:**
* Install the required dependencies: `curl make clang-3.8 nasm bridge-utils qemu`
* Download the latest binary release bundle from github, using the github API.
* Unzip the bundle to `$INCLUDEOS_INSTALL_LOC` - which you can set in advance, or which defaults to `$HOME`
* Create a network bridge called `include0`, for tap-networking
* Build the vmbuilder, which turns your service into a bootable image
* Copy `vmbuild` and `qemu-ifup` from the repo, over to `$INCLUDEOS_HOME`

**Time:**
About a minute or two (On a 4-core virtualbox Ubuntu VM, runing on a 2015 MacBook Air)

## B) Completely build everything from source (slow)
    $ sudo apt-get install git
    $ git clone https://github.com/hioa-cs/IncludeOS
    $ cd IncludeOS
    $ ./install.sh
    
**The script will:**
* Install all the tools required for building IncludeOS, and all libraries it depends on:
  * `build-essential make nasm texinfo clang-3.8 cmake ninja-build subversion zlib1g-dev libtinfo-dev`
* Build a GCC cross compiler along the lines of the [osdev howto](http://wiki.osdev.org/GCC_Cross-Compiler) which we really only need to build `libgcc` and `newlib`.
* Build [Redhat's newlib](https://sourceware.org/newlib/) using the cross compiler, and install it according to `./etc/build_newlib.sh`. The script will also install it to the mentioned location.
* Build a 32-bit version of [LLVM's libc++](http://libcxx.llvm.org/) tailored for IncludeOS. 
* Build and install the IncludeOS library, which your service will be linked with.
* Build and install the `vmbuild` tool, which turns your service into a bootable disk image.

**Time:** 
On a VM with 2 cores and 4 GB RAM, running Ubuntu 14.04, running ./install.sh takes about 33 minutes depending on bandwidth.

**NOTE:** Both scripts will install packages, and as such parts will require sudo access.

### Testing the installation

A successful setup should enable you to build and run a virtual machine. Running the test-script:

    IncludeOS$ ./test.sh 

will build and run a [this example service](./examples/demo_service/service.cpp). 

**Things to note**
* The default test script will only work on Linux, and uses Qemu (with KVM if available). To run IncludeOS directly on VirtualBox, see `etc/vboxrun.sh`
* There is no shell! IncludeOS is a unikernel, and so it will only run one process. Think of an IncludeOS VM as a local process.
* There is no default VGA! So, nothing will show up on the "screen" if you're using a GUI (i.e. if you run IncludeOS directly in virtualbox). To enable VGA you will need to connect `OS::set_rsprint_secondary` to a lambda that calls `ConsoleVGA::write` (see: [api/kernel/vga.hpp](api/kernel/vga.hpp) and the [test service](src/debug/test_service.cpp) for an example), as well as enable graphical output in the emulator, such as qemu. We'll add VGA support in the future, as a package.
* You should be able to ping the VM. Its IP-address will be stated in the boot-time output from IncldueOS 
* You should also be able to open a simple webpage on the VM, by entering the IP into a browser, inside the development machine.
* How to get out? The test script starts [qemu](http://wiki.qemu.org/Main_Page) with the `--nographics`-option. This will by default reroute stdin and stdout to the terminal. To exit the virtual machine, you can go via the [Qemu monitor](https://en.wikibooks.org/wiki/QEMU/Monitor#Virtual_machine). The command for entering the monitor is `Ctrl+a c`, or to exit directly, `Ctrl+a x`.
   * *NOTE*: This keyboard shortcut may not work if you're interacting with your development environment is via a VirtualBox GUI, over putty, inside a `screen` etc. If you find a good solution for a certain platform (i.e. putty to VirtualBox on Windows), please let us know so we can update our wiki.

## Writing a service
Developing IncludeOS services should be done completely separately from IncludeOS repository. All you need is compiled and installed libraries, in your `$INCLUDEOS_HOME` directory (`$HOME/IncludeOS_install` by default). The [./seed](./seed) directory contains everything a service needs to build and link with IncludeOS. 

*NOTE: Don't develop inside the seed directory; the point is to be able to reuse the seed to create new blank services*

### Create a new service by copying the seed

1. Copy the [./seed](./seed) directory to a convenient location like `~/your_service`. You can then start implementing the `Service::start` function in the `Service` class, located in [your_service/service.cpp](./seed/service.cpp) (Very simple example provided). This function will be called once the OS is up and running.  
2. Enter the name of your service in the first line of the [seed Makefile](./seed/Makefile). This will be the base for the name of the final disk image.

**Example:**
```
     $ cp -r seed ~/my_service
     $ cd ~/my_service
     $ emacs service.cpp
     ... add your code
     $ ./run.sh my_service.img
```
Take a look at the [examples](./examples). These all started out as copies of the same seed.

### Helper scripts
There's a convenience script, [./seed/run.sh](./seed/run.sh), which has the "Make-vmbuild-qemu" sequence laid out, with special options for debugging (It will add debugging symbols to the elf-binary and start qemu in debugging mode, ready for connection with `gdb`. More on this inside the script.). I use this script to run the code, where I'd normally just run the program from a shell. Don't worry, it's fast, even in nested/emulated mode.

### Using VirtualBox for development
  * VirtualBox does not support nested virtualization (a [ticket](https://www.virtualbox.org/ticket/4032) has been open for 5 years). This means you can't use the kvm module to run IncludeOS from inside virtualbox, but you can use Qemu directly, so developing for IncludeOS in a virtualbox VM works. It will be slower, but a small VM still boots in no time. For this reason, this install script does not require kvm or nested virtualization.
  * You might want to install Virtual box vbox additions, if you want screen scaling. The above provides the prerequisites for this (compiler stuff). 

### Debugging with Bochs
* If you want to debug the bootloader, or inspect memory, registers, flags etc. using a GUI, you need to install [Bochs](http://bochs.sourceforge.net/). This is because `gdb` only works for objects with debugging symbols, which we don't have for our bootloader. See `./etc/bochs_installation.sh` for build options, and `./etc/.bochsrc` for an example configuration file (which specifies a <1MB disk).

## C++ Guidelines
We are currently far from it, but in time we'd like to adhere as much as possible to the [ISO C++ Core Guidelines](https://github.com/isocpp/CppCoreGuidelines), maintained by the [Jedi Council](https://isocpp.org/). When (not if) you find code in IncludeOS which doesn't adhere, please let us padawans know, in the issue-tracker - or even better, fix it in your own fork and send us a pull-request. 
  * *Note: We're not using the Guidelines Support Library, but we probably will at some point. This means we're not ready to follow guidelines that requires this library yet.*

## Read more on the wiki
We're trying to grow a Wiki, and some questions might already be answered here in the [FAQ](https://github.com/hioa-cs/IncludeOS/wiki/FAQ). 

See the [Wiki front page](https://github.com/hioa-cs/IncludeOS/wiki) for a complete introduction, system overview, and more detailed guides.
