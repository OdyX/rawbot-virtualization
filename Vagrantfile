# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

load 'virtualization/VagrantfileExtra.rb'

custom_config = CustomConfig.new

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    config.vm.box = custom_config.project_name
    config.vm.hostname = custom_config.hostname

    config.vm.box_url = "http://vagrantbox-public.liip.ch/liip-wheezy64.box"

    config.vm.network :private_network, ip: custom_config.box_ip
    config.vm.network "forwarded_port", guest: 80, host: 8080, auto_correct: true

    config.ssh.forward_agent = true

    if Vagrant.has_plugin?("vagrant-hostmanager")
        config.hostmanager.enabled = true
        config.hostmanager.manage_host = true
        config.hostmanager.ignore_private_ip = false
        config.hostmanager.include_offline = true
        config.hostmanager.aliases = custom_config.load_aliases
    end

    config.vm.provider "virtualbox" do |v|
        v.memory = 4096
        v.cpus = 2
        v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    end

    config.vm.provider "lxc" do |lxc, override|
        override.vm.box_url = "http://vagrantbox-public.liip.ch/liip-wheezy64-lxc.box"

        if Vagrant.has_plugin?("vagrant-hostmanager")
            override.hostmanager.ignore_private_ip = true
        end
    end

    config.vm.synced_folder ".", "/vagrant", type: "nfs", nfs_udp: true, nfs_version: 3

    config.vm.provision "ansible" do |ansible|
        ansible.host_key_checking = false
        ansible.playbook = "virtualization/playbook.yml"
        ansible.verbose = 'v'
        ansible.host_key_checking = false
    end
end
