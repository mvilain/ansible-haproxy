# ha-prox-demo -- demo for ha-prox

## Michael Vilain <michael@vilain.com> 2018.0128mev

These notes are for the end-user of the demo and supply directions on how to setup a system to install the demo, install it from files or the Vagrant Hub, and how to run the demo.



## Requirements on the demo system

**Note**: the demo was developed on a Mac Pro with 14GB of memory and a 750GB SSD. Performance on smaller, lesser powered systems may vary. This has **not** been tested on Windows.

1. Install [Virtual Box from Oracle](https://www.virtualbox.org/wiki/Downloads) and the [extensions](https://download.virtualbox.org/virtualbox/5.2.6/Oracle_VM_VirtualBox_Extension_Pack-5.2.6-120293.vbox-extpack)
2. [Download and install Vagrant](https://www.vagrantup.com/downloads.html)
3. [Download and install Ansible](https://hvops.com/articles/ansible-mac-osx/)
4. Create a directory for the demo (e.g. ~/alation)
```
mkdir -p ~/alation 
```

4. Clone the repository with the Vagrant file into the alation directory

```
git clone https://github.com/mvilain/alation.git
```

## Running the Demo

To setup the demo, type

```
vagrant up
```

at the command line.

Vagrant will go through and start the haproxy server and three web clients, web1, web2, and web3.  

* web1 will be running Apache on CentOS 7
* web2 will be running nginx on CentOS 7
* web3 will not be running a web server

The last case is to test the configuration for the load balancer so that it will not direct traffic to a dead web service.



## Cleaning up after the demo

The demo run run as long as the virtual machines are running on your system.  You can halt them by type

```
vagrant halt
```

and all the VMs will stop.

```
$ vagrant halt
==> haproxy: Attempting graceful shutdown of VM...
==> web3: Attempting graceful shutdown of VM...
==> web2: Attempting graceful shutdown of VM...
==> web1: Attempting graceful shutdown of VM...
admin:demo root# vagrant status
Current machine states:

web1                   poweroff (virtualbox)
web2                   poweroff (virtualbox)
web3                   poweroff (virtualbox)
haproxy1               poweroff (virtualbox)
This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
```

If you want to start the demo over again from freshly cloned-systems, use

```
vagrant destroy --force
vagrant up
```

and Vagrant will delete the existing VMs and re-create and provision new ones. Note that if you `halt` VMs, then `vagrant start` them, they'll be in the same state as before they were halted. If any storage was allocated on any of the nodes, it will be there when you restore the demo VMs unless you destroy and re-instatiate new VMs.

