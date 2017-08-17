# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION ||= "2"

Vagrant.require_version '>= 1.9.0'

machines = [
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

# Number of boxes
N = machines.length

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  
  # Disabling the default /vagrant share
  config.vm.synced_folder ".", "/vagrant", disabled: true

  # Using default insecure key
  config.ssh.insert_key = false

  machines.each_with_index do |machine, machine_index|
    config.vm.define machine[:name] do |node|
      node.vm.hostname = machine[:name]
      node.vm.box = machine[:box]
      node.vm.network "private_network", ip: machine[:eth1]

      # Only execute once the Ansible provisioner,
      # when all the machines are up and ready.
      #
      # After defining all machines, the machine_index is
      # equal to N-1, so we need to add 1 otherwise 
      # the provisioning will never be executed
      if machine_index+1 == N

        # Enable provisioning with Ansible.
        node.vm.provision :ansible do |ansible|
          # Verbosity level
          ansible.verbose = "vvv"

          # Disable default limit to connect to all the machines          
          ansible.limit = "all"

          ansible.playbook = "playbook.yml"
        end
      end
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
end
