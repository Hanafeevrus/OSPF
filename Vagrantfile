# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"
  config.vm.box_check_update = true

  config.vm.provision "shell", inline: <<-SHELL
    set -x
  SHELL

  config.vm.define "r1", primary: true do |a|
    a.vm.hostname = 'r1'
    a.vm.network "private_network", ip: "192.168.10.10"
    a.vm.network "private_network", ip: "172.16.15.10"
    a.vm.provision :ansible_local do |r1|
          r1.playbook = 'playbooks/r1.yml'
     end
    end
  config.vm.define "r2" do |b|
    b.vm.hostname = 'r2'
    b.vm.network "private_network", ip: "192.168.10.12"
    b.vm.network "private_network", ip: "10.11.12.12"
    b.vm.provision :ansible_local do |r2|
	    r2.playbook = 'playbooks/r2.yml'
     end
    end

  config.vm.define "r3" do |c|
    c.vm.hostname = 'r3'
    c.vm.network "private_network", ip: "10.11.12.13"
    c.vm.network "private_network", ip: "172.16.15.13"
    c.vm.provision :ansible_local do |r3|
	    r3.playbook = 'playbooks/r3.yml'
   end
  end
end
