# -*- mode: ruby -*-
# vi: set ft=ruby :

# alation Vagrant file to spin up multiple machines
# Maintainer Michael Vilain [201801.26]


Vagrant.configure(2) do | config |
	# For a complete reference, please see the online documentation at
	# https://docs.vagrantup.com.

	# config.vm.box = 'centos/7'
	config.vm.box = 'geerlingguy/centos7'
	# config.vm.network 'forwarded_port', guest: 80, host: 8080
	config.ssh.insert_key = false
	config.vm.synced_folder '.', '/vagrant', disabled: false
	config.vm.provider :virtualbox do |vb|
		#vb.gui = true
		vb.memory = '1024'
	end

	# provision on all machines -- set hosts and allow ssh w/o checking
	config.vm.provision "shell", inline: <<-SHELLALL
		cat /vagrant/etc-hosts >> /etc/hosts
		sed -i.orig -e "s/#   CheckHostIP yes/CheckHostIP no/" /etc/ssh/ssh_config
	SHELLALL

	# run ansible playbook on all nodes...node-specific stuff is in the playbook
    config.vm.provision "ansible" do |ansible|
		ansible.compatibility_mode = "2.0"
		ansible.playbook = "playbook-cent.yaml"
		ansible.inventory_path = "./inventory"
		# ansible.verbose = "v"
		# ansible.raw_arguments = [""]
    end

	config.vm.define "web1" do | web1 | 
		web1.vm.network 'private_network', ip: '192.168.10.101'
		web1.vm.hostname = 'web1'
		# web1.vm.provision "shell", inline: <<-SHELL1
		# SHELL1
	end

	config.vm.define "web2" do | web2 |
		web2.vm.network 'private_network', ip: '192.168.10.102'
		web2.vm.hostname = 'web2'
		# web2.vm.provision "shell", inline: <<-SHELL2
		# SHELL2
	end

	config.vm.define "web3" do | web3 |
		web3.vm.network 'private_network', ip: '192.168.10.103'
		web3.vm.hostname = 'web3'
		# web3.vm.provision "shell", inline: <<-SHELL3
		# SHELL3
	end

	config.vm.define "haproxy1", primary: true do | haproxy1 |
		haproxy1.vm.network 'private_network', ip: '192.168.10.100'
		haproxy1.vm.hostname = 'haproxy1'
		# haproxy1.vm.network 'forwarded_port', guest: 80, host: 8080
		# haproxy1.vm.provision "shell", inline: <<-SERVER
		# SERVER
	end

end
