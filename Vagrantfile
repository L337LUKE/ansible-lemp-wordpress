# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # The box we want to pull in, in this case ubuntu 14.04
  config.vm.box = "ubuntu/trusty64"

  config.vm.provider "virtualbox" do |v|
    v.memory = 2048
    v.cpus = 2
  end

  # Forward ports so we can test nginx and that frontends load
  config.vm.network "forwarded_port", guest: 80, host: 8080
  
  # Assign a private ip address for us to connect to
  config.vm.network "private_network", ip: "192.168.50.5"
  
  # Disable the new default behavior introduced in Vagrant 1.7, to
  # ensure that all Vagrant machines will use the same SSH key pair.
  # See https://github.com/mitchellh/vagrant/issues/5005
  config.ssh.insert_key = false

  config.vm.provision "ansible_local" do |ansible|
    ansible.galaxy_role_file    = "requirements.yml"
    ansible.playbook            = 'provision.yml'
    ansible.inventory_path      = "hosts"
    ansible.verbose             = true
    ansible.install             = true
  end
end
