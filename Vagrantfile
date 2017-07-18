# -*- mode: ruby -*-
# vi: set ft=ruby :

# alation Vagrant file to spin up multiple machines
# Maintainer Michael Vilain [201707.17]

$server_script = <<EOFHAPROXY
	echo "provisioning haproxy"
	yum update -y
	yum install -y haproxy
	# populate config files here
	# start HAproxy

EOFHAPROXY

$config_httpd = <<EOFCLIENTHTTPD
	echo "providing apache"
	yum update -y
	yum install -y httpd
	cat <<CONFIG > /tmp/http.conf

CONFIG
EOFCLIENTHTTPD

$config_nginx = <<EOFCLIENTNGINX
	echo "provisioning nginx"
	yum install -y epel-release
	yum install -y nginx
	# populate base html

EOFCLIENTNGINX

Vagrant.configure(2) do | config |
	# For a complete reference, please see the online documentation at
	# https://docs.vagrantup.com.

	#config.vm.box = "centos/7"
	# config.vm.box_check_update = false
	# config.vm.network "forwarded_port", guest: 80, host: 8080

	config.vm.define "haproxy", primary: true do | haproxy |
		haproxy.vm.box = "centos/7"

		#haproxy.vm.network "forwarded_port", guest: 80, host: 8080
		haproxy.vm.network "public_network", ip: "192.168.10.100", bridge: "en2: Wi-Fi (AirPort)"

		#haproxy.vm.synced_folder "demo-haproxy", "/vagrant"
		#haproxy.ssh.insert_key = false
		haproxy.vm.provision "shell", inline: <<-SERVER
			echo "setting hostname=demo-haproxy"
			echo "demo-haproxy" > /etc/hostname
			echo "192.168.10.100 demo-haproxy" >> /etc/hosts
			ip addr
		SERVER

		haproxy.vm.provision "shell", inline: $server_script
		#haproxy.vm.provider "virtualbox" do |vb|
		#  # Display the VirtualBox GUI when booting the machine
		#  vb.gui = true
		#
		#  # Customize the amount of memory on the VM:
		#  vb.memory = "1024"
		#end
	end

	config.vm.define "web1" do | web1 |
		web1.vm.box = "centos/7"
		#web1.vm.network "forwarded_port", guest: 80, host: 8081
		web1.vm.network "public_network", ip: "192.168.10.101", bridge: "en2: Wi-Fi (AirPort)"
		#web1.vm.synced_folder "demo-web1", "/vagrant"

		web1.vm.provision "shell", inline: <<-SHELL1
			echo "setting hostname=demo-web1"
			echo "demo-web1" > /etc/hostname
			echo "192.168.10.101 demo-web1" >> /etc/hosts
			ip addr
		SHELL1

		web1.vm.provision "shell", inline: $config_httpd
		#web1.vm.provision "shell", inline: $config_nginx

		#web1.vm.provider "virtualbox" do |vb|
		#  # Display the VirtualBox GUI when booting the machine
		#  vb.gui = true
		#
		#   # Customize the amount of memory on the VM:
		#  vb.memory = "1024"
		#end
	end

	config.vm.define "web2" do | web2 |
		web2.vm.box = "centos/7"
		web2.vm.network "forwarded_port", guest: 80, host: 8082
		web2.vm.network "public_network", ip: "192.168.10.102", bridge: "en2: Wi-Fi (AirPort)"
		#web2.vm.synced_folder "demo-web2", "/vagrant"

		web2.vm.provision "shell", inline: <<-SHELL2
			echo "setting hostname=demo-web2"
			echo "demo-web2" > /etc/hostname
			echo "192.168.10.102 demo-web2" >> /etc/hosts
			ip addr
		SHELL2

		#web2.vm.provision "shell", inline: $config_httpd
		web2.vm.provision "shell", inline: $config_nginx

		#web2.vm.provider "virtualbox" do |vb|
		#  # Display the VirtualBox GUI when booting the machine
		#  vb.gui = true
		#
		#   # Customize the amount of memory on the VM:
		#  vb.memory = "1024"
		#end
	end

	config.vm.define "web3" do | web3 |
		web3.vm.box = "centos/7"
		web3.vm.network "forwarded_port", guest: 80, host: 8083
		web3.vm.network "public_network", ip: "192.168.10.103", bridge: "en2: Wi-Fi (AirPort)"
		#web3.vm.synced_folder "demo-web3", "/vagrant"

		web3.vm.provision "shell", inline: <<-SHELL3
			echo "setting hostname=demo-web3"
			echo "demo-web3" > /etc/hostname
			echo "192.168.10.103 demo-web3" >> /etc/hosts
			ip addr
		SHELL3

		#web3.vm.provision "shell", inline: $config_httpd
		#web3.vm.provision "shell", inline: $config_nginx
		#web3.vm.provider "virtualbox" do |vb|
		#  # Display the VirtualBox GUI when booting the machine
		#  vb.gui = true
		#
		#   # Customize the amount of memory on the VM:
		#  vb.memory = "1024"
		#end
	end

end
