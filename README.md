vmm - Virtual Machine Manager
=====

# Overview


Virtual Machine Manager (vvm) is a bunch of wrapper scripts on top of existing Virtualization softwares. Vvm makes it easier to start and stop such machines and execute program (typically to perform tests) on these virtual machines.

Vvm is written in Bash and uses SSH to communicate/execute programs on the various machines.


Vvm is part of Free Software Client Reference System (FSCRS). FSCRS is a project at TIS Innovation Park in South Tyrol.


# Abbreviations

*Machine* - A virtual image as accessible by the supported virtualization softwares (e.g Virtualbox, qemu). A machine may be used by several clients (or none). 

*Client* - A system configuration used by vvm to manage machines. Each client must belong to one (and only) one machine for the client. Separating clients from machines makes it easy for you to use one machine (the actual runnable virtual image) in many clients with different setups such as user, log files and timeouts.


# Required software
 
* ssh (client)

* at least one virtualization software (see list of supported softwares below)

* bash


# Supported Virtualization software

* Virtualbox

* qemu

We're looking into supporting: vmware, 

# Installation

* Download git code

  `git://github.com/tis-innovation-park/vvm.git`

* Go to the vvm directory

  `cd vvm`

* Configure the software

  `./configure --prefix <installationdir>`

* Build the software

  `make`

* Install the software

  `make install`

* Verify the installation

  `<installationdir>/bin/ats-client --list-clients`


# Setup

* Create the directory $HOME/.ats

  `mkdir $HOME/.ats`

* Create ats.conf in .ats, typically with your favorite editor (emacs?)

  `emacs ~/.ats/ats.conf`

In this file you can configure settings you want to use as default in your clients. It is perfectly possible to override these settings in your individual client configurations.

For a list of variables, see section Configuration syntax below.



## Creating a machine

To create a machine vvm relies on the virtualization software. So if you want to manage a Virtualbox machine you (at least for now) create it with Virtualbox.


## Creating a client

First of all you need to decide what machine you want to use with your client. In the example below we will assume it is called Debian6.0


* Create a directory for all clients:

  `mkdir ~/.ats/clients`

* Create a configuration file for the client (using a virtual machine):

  [CLIENT NAME].conf

* Set the variables as you find suitable for your project.

* Example of a client configuration:

`
   VM_NAME="Debian6.0"

   VM_TYPE="VirtualBox"

   VM_IP_ADDRESS=192.168.1.2

   VM_USER=$USER

   VM_SUPERUSER=root

   SSH_PORT=22

   SSH_SHUTDOWN_COMMAND="shutdown -h now"

`

 For more variables see section Configuration syntax below

* Copy the public ssh key to the machine, e.g

  `ssh-copy-id 192.168.1.2`


# Using VVM



## Client management

*Command line options*:

`  --list-clients`

lists all configured clients

`  --start-clients client`

starts client 

`  --stop-clients client`

stops client

## Machine management

`  --list-machine client`

lists all machines known to VVM

`  --start-machine client`

starts machine 

`  --stop-machine client` 

stops machine




# Configuration syntax

The syntax for setting a variable is the same as in bash scrips (no coincidence!). Basically you write:

`VARIABLE=VALUE`

## Variables

`LOG_FILE_DIR=/tmp/ats/log` - sets the log file base directory to /tmp/ats/log. This means that all logs can be found here.

`VM_STARTUP_TIMEOUT=10` - the time to wait for a virtual machine to start up before considering it to be 'dead'.

`VM_STOP_TIMEOUT=20` - the time to wait for a virtual machine to start up before taking more drastic actions to take down the machine. Ultimately VVM will take down a machine with a `kill`.

`SSH=ssh` - the SSH program to use

`SSH_TEST_OPTIONS=" -o connectTimeout=3"` - the options to pass to the SSH program (see SSH variable above) when testing of the machine is up (or not).

`VM_NAME="Debian6.0"` - the name of the machine to use in this client. It is important that your Virtualization software can find this machine.

`VM_TYPE="VirtualBox"` - what type of Virtulization software this machine belongs to. Currently you can use: VirtualBox and qemu

`VM_IP_ADDRESS=192.168.x.x` - the IP address of the virtual machine 

`VM_USER=$USER` - the user you want to use when accessing the virtual machine

`VM_SUPERUSER=root` - the name of the super user/administrator account, typically used to reboot the machine 

`SSH_PORT=22` - the port where the SSH server is running on the client

`SSH_SHUTDOWN_COMMAND="shutdown -h now"` - VVM will do its very best to shut down a machine as gracefully as possible. One way to do this is to try to shut it down using SSH. The command in this variable will be used to do that.

# OBSOLETED TEXT BELOW

Basic Setup (currently not in sync)
----------------------

* create dir $HOME/.ats

  `mkdir $HOME/.ats`

* create ats.conf in .ats, typically with your favorite editor (emacs?)

  `emacs ~/.ats/ats.conf`

* add folling to file:

# ATS Settings
  LOG_FILE_DIR=/tmp/ats/log

# ATSVM Settings
  VM_STARTUP_TIMEOUT=300
  VM_STOP_TIMEOUT=20
  SSH=ssh
  SSH_TEST_OPTIONS=" -o connectTimeout=3"

* copy public key to /root/.ssh/authorized_keys

 ./configure --prefix <install dir>

* Try it out:

 /tmp/ats-tmp-install/bin/ats-client --list-clients

Usage:

/tmp/ats-tmp-install/ats-client --check-client-status  $VBOXNAME
/tmp/ats-tmp-install/bin/ats-client --check-client-status  $VBOXNAME
/tmp/ats-tmp-install/bin/ats-client --check-client-ssh  $VBOXNAME
/tmp/ats-tmp-install/bin/ats-client --client-exec $VBOXNAME "pkcs15-tool -L"

