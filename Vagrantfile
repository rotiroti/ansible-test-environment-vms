# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION ||= "2"

Vagrant.require_version '>= 1.9.0'

boxes = [
  {
    :name => "trusty",
    :box => "ubuntu/trusty64",
    :eth1 => "192.168.100.2"
  },
  {
    :name => "centos7",
    :box => "centos/7",
    :eth1 => "192.168.100.3"
  }  
]

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  
  # Disabling the default /vagrant share
  config.vm.synced_folder ".", "/vagrant", disabled: true

  # Using default insecure key
  config.ssh.insert_key = false

  boxes.each do |b|
    config.vm.define b[:name] do |config|
      config.vm.hostname = b[:name]
      config.vm.box = b[:box]
      config.vm.network "private_network", ip: b[:eth1]
    end
  end

  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    vb.gui = false
  
    # Customize the amount of memory on the VM:
    vb.memory = "1024"

    # Customize the amount of cpus on the VM:
    vb.cpus = 1

    # Change the network card hardware for better performance
    vb.customize ["modifyvm", :id, "--nictype1", "virtio" ]
    vb.customize ["modifyvm", :id, "--nictype2", "virtio" ]

    # Force DNS requests by the VM to use the host DNS, rather than
    # external DNS servers
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]    
  end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "ansible" do |ansible|
    ansible.verbose = "vvv"
    ansible.limit = "all"
    ansible.playbook = "playbook.yml"
  end
end
