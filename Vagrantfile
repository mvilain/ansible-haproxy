# -*- mode: ruby -*-
# vi: set ft=ruby :

# alation Vagrant file to spin up multiple machines
# Maintainer Michael Vilain [201707.17]

$server_script = <<EOFHAPROXYINSTALL
	echo "provisioning haproxy"
	yum update -y
	yum install -y haproxy ${TOOLS}
	cd /etc/haproxy
	/bin/mv haproxy.cfg haproxy.cfg.orig
	cat <<EOFHACONFIG > haproxy.cfg
# https://www.howtoforge.com/tutorial/how-to-setup-haproxy-as-load-balancer-for-nginx-on-centos-7/
# https://serversforhackers.com/c/load-balancing-with-haproxy
#---------------------------------------------------------------------
# Global settings
#---------------------------------------------------------------------
global
    log         127.0.0.1 local2     #Log configuration

    chroot      /var/lib/haproxy
    pidfile     /var/run/haproxy.pid
    maxconn     4000                
    user        haproxy             #Haproxy running under user and group "haproxy"
    group       haproxy
    daemon
 
    # turn on stats unix socket
    stats socket /var/lib/haproxy/stats
 
#---------------------------------------------------------------------
# common defaults that all the 'listen' and 'backend' sections will
# use if not designated in their block
#---------------------------------------------------------------------
defaults
    mode                    http
    log                     global
    option                  httplog
    option                  dontlognull
    option http-server-close
    option forwardfor       except 127.0.0.0/8
    option                  redispatch
    retries                 3
    timeout http-request    10s
    timeout queue           1m
    timeout connect         10s
    timeout client          1m
    timeout server          1m
    timeout http-keep-alive 10s
    timeout check           10s
    maxconn                 3000
 
#---------------------------------------------------------------------
#HAProxy Monitoring Config
#---------------------------------------------------------------------
listen haproxy3-monitoring *:8080                #Haproxy Monitoring run on port 8080
    mode http
    option forwardfor
    option httpclose
    stats enable
    stats show-legends
    stats refresh 5s
    stats uri /stats                            #URL for HAProxy monitoring
    stats realm Haproxy\ Statistics
    stats auth howtoforge:howtoforge            #User and Password for login to the monitoring dashboard
    stats admin if TRUE
    default_backend app-main                    #This is optionally for monitoring backend
 
#---------------------------------------------------------------------
# FrontEnd Configuration
#---------------------------------------------------------------------
frontend main
    bind *:80
    option http-server-close
    option forwardfor
    default_backend nodes
 
#---------------------------------------------------------------------
# BackEnd roundrobin as balance algorithm
#---------------------------------------------------------------------
backend nodes
    balance roundrobin                                   # Balance algorithm
    option httpchk HEAD / HTTP/1.1\\r\\nHost:localhost   #Check the server application is up and healty - 200 status code
    server web2 192.168.10.102:80 check                  # web2
    server web1 192.168.10.101:80 check                  # web1 
    server web3 192.168.10.103:80 check                  # web3

EOFHACONFIG

	cd /etc
	/bin/mv rsyslog.conf rsyslog.conf.orig
	cat <<'EOFRSYSLOG1' >rsyslog.conf	# turn off parameter substitution
# rsyslog configuration file

# For more information see /usr/share/doc/rsyslog-*/rsyslog_conf.html
# If you experience problems, see http://www.rsyslog.com/doc/troubleshoot.html

#### MODULES ####

# The imjournal module bellow is now used as a message source instead of imuxsock.
$ModLoad imuxsock # provides support for local system logging (e.g. via logger command)
$ModLoad imjournal # provides access to the systemd journal
#$ModLoad imklog # reads kernel messages (the same are read from journald)
#$ModLoad immark  # provides --MARK-- message capability

# Provides UDP syslog reception
$ModLoad imudp
$UDPServerRun 514
$UDPServerAddress 127.0.0.1

# Provides TCP syslog reception
#$ModLoad imtcp
#$InputTCPServerRun 514


#### GLOBAL DIRECTIVES ####

# Where to place auxiliary files
$WorkDirectory /var/lib/rsyslog

# Use default timestamp format
$ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat

# File syncing capability is disabled by default. This feature is usually not required,
# not useful and an extreme performance hit
#$ActionFileEnableSync on

# Include all config files in /etc/rsyslog.d/
$IncludeConfig /etc/rsyslog.d/*.conf

# Turn off message reception via local log socket;
# local messages are retrieved through imjournal now.
$OmitLocalLogging on

# File to store the position in the journal
$IMJournalStateFile imjournal.state


#### RULES ####

# Log all kernel messages to the console.
# Logging much else clutters up the screen.
#kern.*                                                 /dev/console

# Log anything (except mail) of level info or higher.
# Don't log private authentication messages!
*.info;mail.none;authpriv.none;cron.none                /var/log/messages

# The authpriv file has restricted access.
authpriv.*                                              /var/log/secure

# Log all the mail messages in one place.
mail.*                                                  -/var/log/maillog


# Log cron stuff
cron.*                                                  /var/log/cron

# Everybody gets emergency messages
*.emerg                                                 :omusrmsg:*

# Save news errors of level crit and higher in a special file.
uucp,news.crit                                          /var/log/spooler

# Save boot messages also to boot.log
local7.*                                                /var/log/boot.log


# ### begin forwarding rule ###
# The statement between the begin ... end define a SINGLE forwarding
# rule. They belong together, do NOT split them. If you create multiple
# forwarding rules, duplicate the whole block!
# Remote Logging (we use TCP for reliable delivery)
#
# An on-disk queue is created for this action. If the remote host is
# down, messages are spooled to disk and sent when it is up again.
#$ActionQueueFileName fwdRule1 # unique name prefix for spool files
#$ActionQueueMaxDiskSpace 1g   # 1gb space limit (use as much as possible)
#$ActionQueueSaveOnShutdown on # save messages to disk on shutdown
#$ActionQueueType LinkedList   # run asynchronously
#$ActionResumeRetryCount -1    # infinite retries if host is down
# remote host is: name/ip:port, e.g. 192.168.0.1:514, port optional
#*.* @@remote-host:514
# ### end of the forwarding rule ###
EOFRSYSLOG1

	echo '$UDPServerAddress 127.0.0.1' >>/etc/rsyslog.conf
	cat <<EOFRSYSLOG2 > /etc/rsyslog.d/haproxy.conf
local2.=info     /var/log/haproxy-access.log    #For Access Log
local2.notice    /var/log/haproxy-info.log      #For Service Info - Backend, loadbalancer
EOFRSYSLOG2
	systemctl restart rsyslog

	systemctl start haproxy
	systemctl enable haproxy

EOFHAPROXYINSTALL

$config_httpd = <<EOFCLIENTHTTPD
	echo "providing apache"
	yum update -y
	yum install -y httpd
	sed -i.orig -s "s/^Listen 80/Listen 0.0.0.0:80/" /etc/httpd/conf/httpd.conf
	systemctl start httpd
	systemctl enable httpd

EOFCLIENTHTTPD

$config_nginx = <<EOFCLIENTNGINX
	echo "provisioning nginx"
	yum install -y epel-release
	yum install -y nginx
	systemctl start nginx
	systemctl enable nginx

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
		haproxy.vm.network "private_network", ip: "192.168.10.100"

		#haproxy.vm.synced_folder "haproxy", "/vagrant"

		haproxy.vm.provision "shell", inline: <<-SERVER
			echo "setting hostname=demo-haproxy"
			echo "demo-haproxy" > /etc/hostname
			echo "192.168.10.100 haproxy" >> /etc/hosts
			echo "192.168.10.101 web1" >> /etc/hosts
			echo "192.168.10.102 web2" >> /etc/hosts
			echo "192.168.10.103 web3" >> /etc/hosts
			hostname haproxy
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
		web1.vm.network "private_network", ip: "192.168.10.101"

		web1.vm.provision "shell", inline: <<-SHELL1
			echo "setting hostname=web1"
			echo "web1" > /etc/hostname
			hostname web1
			echo "192.168.10.100 haproxy" >> /etc/hosts
			echo "192.168.10.101 web1" >> /etc/hosts
			echo "192.168.10.102 web2" >> /etc/hosts
			echo "192.168.10.103 web3" >> /etc/hosts
			ip addr
		SHELL1

		web1.vm.provision "shell", inline: $config_httpd
		#web1.vm.provision "shell", inline: $config_nginx

	end

	config.vm.define "web2" do | web2 |
		web2.vm.box = "centos/7"
		web2.vm.network "private_network", ip: "192.168.10.102"

		web2.vm.provision "shell", inline: <<-SHELL2
			echo "setting hostname=web2"
			echo "web2" > /etc/hostname
			hostname web2
			echo "192.168.10.100 haproxy" >> /etc/hosts
			echo "192.168.10.101 web1" >> /etc/hosts
			echo "192.168.10.102 web2" >> /etc/hosts
			echo "192.168.10.103 web3" >> /etc/hosts
			ip addr
		SHELL2

		#web2.vm.provision "shell", inline: $config_httpd
		web2.vm.provision "shell", inline: $config_nginx

	end

	config.vm.define "web3" do | web3 |
		web3.vm.box = "centos/7"
		#web3.vm.network "forwarded_port", guest: 80, host: 8083
		web3.vm.network "private_network", ip: "192.168.10.103"
		#web3.vm.synced_folder "web3", "/vagrant"

		web3.vm.provision "shell", inline: <<-SHELL3
			echo "setting hostname=web3"
			echo "web3" > /etc/hostname
			hostname web3
			echo "192.168.10.100 haproxy" >> /etc/hosts
			echo "192.168.10.101 web1" >> /etc/hosts
			echo "192.168.10.102 web2" >> /etc/hosts
			echo "192.168.10.103 web3" >> /etc/hosts
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
