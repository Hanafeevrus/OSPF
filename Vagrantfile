# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"
  config.vm.box_check_update = true

  config.vm.provision "shell", inline: <<-SHELL
    set -x
    	sudo yum update
        sudo yum install epel-release -y	
	sudo yum install net-tools ifconfig traceroute tcpdump quagga -y
	sudo sed -i 's/^SELINUX=.*/SELINUX=disabled/g' /etc/selinux/config	
	sudo sed -i '65s/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
  SHELL

  config.vm.define "r1", primary: true do |a|
    a.vm.hostname = 'r1'
    a.vm.network "private_network", ip: "192.168.10.10"
    a.vm.network "private_network", ip: "172.16.15.10"
    a.vm.provision "shell", inline: <<-SHELL
    sudo cp /vagrant/r1.conf /etc/quagga/ospfd.conf
    sudo echo net.ipv4.conf.eth2.rp_filter = 0 >> /etc/sysctl.conf
    sudo echo net.ipv4.conf.eth1.rp_filter = 0 >> /etc/sysctl.conf
    sudo echo net.ipv4.ip_forward = 1 >> /etc/sysctl.conf
    sudo sysctl -p
    sudo service ospfd restart
    sudo systemctl enable ospfd.service
    SHELL
  end

  config.vm.define "r2" do |b|
    b.vm.hostname = 'r2'
    b.vm.network "private_network", ip: "192.168.10.12"
    b.vm.network "private_network", ip: "10.11.12.12"
    b.vm.provision "shell", inline: <<-SHELL
    sudo cp /vagrant/r2.conf /etc/quagga/ospfd.conf
    sudo echo net.ipv4.conf.eth2.rp_filter = 0 >> /etc/sysctl.conf
    sudo echo net.ipv4.conf.eth1.rp_filter = 0 >> /etc/sysctl.conf
    sudo echo net.ipv4.ip_forward = 1 >> /etc/sysctl.conf
    sudo sysctl -p
    sudo service ospfd restart
    sudo systemctl enable ospfd.service
    SHELL
   end

  config.vm.define "r3" do |c|
    c.vm.hostname = 'r3'
    c.vm.network "private_network", ip: "10.11.12.13"
    c.vm.network "private_network", ip: "172.16.15.13"
    c.vm.provision "shell", inline: <<-SHELL
    sudo cp /vagrant/r3.conf /etc/quagga/ospfd.conf
    sudo echo net.ipv4.conf.eth2.rp_filter = 0 >> /etc/sysctl.conf
    sudo echo net.ipv4.conf.eth1.rp_filter = 0 >> /etc/sysctl.conf
    sudo echo net.ipv4.ip_forward = 1 >> /etc/sysctl.conf
    sudo sysctl -p
    sudo service ospfd restart
    sudo systemctl enable ospfd.service
    SHELL
   end

  end
