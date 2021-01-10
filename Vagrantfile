# -*- mode: ruby -*-
# vi: set ft=ruby :

# Amount of memory in MiB to assign the VM
VM_MEM=8096

# IP address of the VM. Must be in a dedicated subnet, that's not yet routed
# on your host.
VM_IPADDR="192.168.254.2"

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/groovy64"
  config.vm.hostname = "argocd-nutshell"
  config.vm.define "argocd-nutshell"

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: VM_IPADDR

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder ".", "/vagrant"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
    vb.name = "argocd-nutshell"
    vb.memory = VM_MEM
  end

  # Install core requirements for running ansible
  config.vm.provision "shell" do |s|
    s.inline = <<-SHELL
      apt-get update
      apt-get install -y python3-pip
      pip3 install ansible=="2.9.16"
      pip3 install openshift
    SHELL
  end

  # Provision the box using a local ansible. 
  config.vm.provision "ansible_local" do |a|
    a.playbook = "argocd-nutshell.yaml"
    a.become = true
    a.extra_vars = "config/#{ENV['ARGOCD_VARIANT'] || 'default'}.yaml"
  end
end
