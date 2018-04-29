# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = '2'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
#  config.vm.box = 'geerlingguy/centos6'
  config.vm.box = "centos/7"
  config.vm.network :forwarded_port, guest: 80, host: 8080
  config.vm.network :forwarded_port, guest: 3306, host: 3306
  config.vm.hostname = 'lamp'

  config.vm.synced_folder '.', '/var/www/vagrant', mount_options: ['dmode=0775', 'fmode=0664']

  # Set name
  config.vm.define :lamp do |lamp|
  end

  config.vm.provider :virtualbox do |vb|
    vb.name = 'lamp'
  end

  config.vm.provision :ansible_local do |ansible|
    ansible.provisioning_path = '/var/www/vagrant'
    ansible.playbook       = 'provisioning/playbook.yml'
    ansible.verbose        = true
    ansible.install        = true
    ansible.limit          = 'all'
    ansible.galaxy_role_file = 'requirements.yml'
    ansible.inventory_path = 'provisioning/inventory'
  end
end
