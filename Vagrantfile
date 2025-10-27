# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Box: Ubuntu 20.04 
  config.vm.box = "geerlingguy/ubuntu2004"
  config.vm.hostname = "ansible-target"
  
  # Network: Private IP for Ansible connectivity
  config.vm.network "private_network", ip: "192.168.56.10"
  
  # Provisioner: Link to the Ansible playbook
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
    ansible.inventory_path = "inventory.yml"
    ansible.limit = "ansible-target"
  end
end