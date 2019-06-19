# ha-prox-demo -- demo for ha-prox

## Michael Vilain <michael@vilain.com> 201906.18mev

These notes are for the end-user of the demo and supply directions on how to 
setup a system to install the demo, install it from files or the Vagrant Hub, 
and how to run the demo.



## Requirements on the demo system

**Note**: the demo was developed on a Mac Pro with 64GB of memory and a 1TB SSD. 
Performance on smaller, lesser powered systems may vary. This has **not** been tested 
on Windows.

1. Install [Virtual Box from Oracle](https://www.virtualbox.org/wiki/Downloads) 
and the [extensions](https://download.virtualbox.org/virtualbox/5.2.30/Oracle_VM_VirtualBox_Extension_Pack-5.2.30-130521.vbox-extpack)
2. [Download and install Vagrant](https://www.vagrantup.com/downloads.html)
3. [Download and install Ansible](https://hvops.com/articles/ansible-mac-osx/)
4. Create a directory for the demo (e.g. ~/haproxy)
```
mkdir -p ~/haproxy 
```

5. Clone the repository with the Vagrant file into the haproxy directory

```
git clone https://github.com/mvilain/haproxy.git
```

## Running the Demo

To setup the demo, type

```
vagrant up --no-provision
ansible-playbook site.yml
```

at the command line.

Vagrant will go through and start three web clients and the haproxy load balancer.  
It will use ansible to provision all the instances. Note the section were common 
packages are installed and configured in all the servers in parallel.

* web1 will be running apache on CentOS 7
* web2 will be running nginx on CentOS 7
* lbr1 will be running the haproxy load balancer

To test the load balancer, type the following:

```
for t in 1 2 3 4; do echo "--- test \# ${t}"; curl -s http://192.168.10.100 | egrep web[12]; done

vagrant halt web1
for t in 1 2 3 4; do echo "--- test \# ${t}"; curl -s http://192.168.10.100 | egrep web[12]; done

vagrant up web1
for t in 1 2 3 4; do echo "--- test \# ${t}"; curl -s http://192.168.10.100 | egrep web[12]; done

# BONUS: view the following in a local browser: http://192.168.10.100:81
```

You will note that after halting web1, when the load balancer attempts to access it
for the first time it takes time to timeout and cycle to the next web server. After
that, it continues.


## Cleaning up after the demo

The demo run run as long as the virtual machines are running on your system.  
You can halt them by typing

```
vagrant halt
```

and all the VMs will stop.

```
$ vagrant halt
==> lbr1: Attempting graceful shutdown of VM...
==> web2: Attempting graceful shutdown of VM...
==> web1: Attempting graceful shutdown of VM...
admin:demo root# vagrant status
Current machine states:

web1                   poweroff (virtualbox)
web2                   poweroff (virtualbox)
lbr1                   poweroff (virtualbox)
This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
```

If you want to start the demo over again from freshly cloned-systems, use

```
vagrant destroy --force
vagrant up
```

and Vagrant will delete the existing VMs and re-create and provision new ones. 
Note that if you `halt` VMs, then `vagrant start` them, they'll be in the same 
state as before they were halted. If any storage was allocated on any of the nodes, 
it will be there when you restore the demo VMs unless you destroy and re-instatiate 
new VMs.
