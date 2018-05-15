# Creating a LAMP Stack using ansible

## Setup

You should already have VirtualBox and Vagrant installed on your machine. If you don't, please follow these instructions:

### Windows:

### Mac:

### Linux:

You should be able to find the package that suits your OS on the USB stick at /VBox/Linux.

Install using e.g.: `sudo yum install -y <file>.rpm` or `sudo dpkg -i <file>.deb`

Confirm that vagrant is up and running by running `vagrant` - you should see:
```
Usage: vagrant [options] <command> [<args>]

    -v, --version                    Print the version and exit.
    -h, --help                       Print this help.

Common commands:
     box             manages boxes: installation, removal, etc.
     connect         connect to a remotely shared Vagrant environment
     destroy         stops and deletes all traces of the vagrant machine
     global-status   outputs status Vagrant environments for this user
     halt            stops the vagrant machine
     help            shows the help for a subcommand
     init            initializes a new Vagrant environment by creating a Vagrantfile
     login           log in to HashiCorp's Vagrant Cloud
     package         packages a running vagrant environment into a box
     plugin          manages plugins: install, uninstall, update, etc.
     port            displays information about guest port mappings
     powershell      connects to machine via powershell remoting
     provision       provisions the vagrant machine
     push            deploys code in this environment to a configured destination
     rdp             connects to machine via RDP
     reload          restarts vagrant machine, loads new Vagrantfile configuration
     resume          resume a suspended vagrant machine
     share           share your Vagrant environment with anyone in the world
     snapshot        manages snapshots: saving, restoring, etc.
     ssh             connects to machine via SSH
     ssh-config      outputs OpenSSH valid configuration to connect to the machine
     status          outputs status of the vagrant machine
     suspend         suspends the machine
     up              starts and provisions the vagrant environment
     validate        validates the Vagrantfile
     version         prints current and latest Vagrant version

For help on any individual command run `vagrant COMMAND -h`

Additional subcommands are available, but are either more advanced
or not commonly used. To see all subcommands, run the command
`vagrant list-commands`.
```

We're going to do the majority of the work for this workshop on the command line, but you will need to do some editing of files. Since this will be a platform-independent workshop, I will leave it to you to choose the directory that you're going to use - although I'll recommend a folder called `vagrantLAMP/` somewhere under your home area.

I'll also leave the choice of text editor to you. I'll be using Atom...

## 1. The vbguest plugin
We're going to start by installing the vagrant-vbguest plugin. This plugin automatically downloads and installs the correct version of the VirtualBox guest additions into your guest machine. To do this type the following command:
```
vagrant plugin install vagrant-vbguest
```
You should see:
```
Installing the 'vagrant-vbguest' plugin. This can take a few minutes...
Fetching: vagrant-share-1.1.9.gem (100%)
Installed the plugin 'vagrant-vbguest (0.15.1)'!
```

## 2. The base box
We're going to start by creating a really simple Vagrantfile that initialises a Centos 7 box. In order to keep network traffic down a little, we've provided a local copy of the 'ubuntu/xenial64' box. On your USB drive you'll see a folder `Vagrant/`, and within that a folder called `boxes/`. Copy this to the `.vagrant.d/` folder in your home area.

Confirm that this base box is available by running `vagrant box list` - you should see:
```
ubuntu/xenial64 (virtualbox, 1803.01)
```

This shows the box name, type (provider) and the Vagrant version assigned to the box.

## 3. The initial Vagrantfile for the base box.
Create a Vagrantfile (literally, a file called 'Vagrantfile' with the following content:
```
# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = '2'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/xenial64"
  # Set name
  config.vm.hostname = 'lamp'
  config.vm.define :lamp  do |lamp|
  end
  config.vm.provider :virtualbox do |vb|
    vb.name = 'lamp'
  end
end
```
( You can find this initial Vagrantfile in the LAMP folder on the USB stick, if you can't be bothered with the typing!)

Next, start the base box with the command `vagrant up`.

You should see this:
```
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'ubuntu/xenial64'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'ubuntu/xenial64' is up to date...
==> default: Setting the name of the VM: LAMP_default_1525729225708_92581
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
[...]
```
There will be a lot more text output than that, as the vbguest plugin installs the packages that it needs. You may also see an error during installation of the VirtualBox Guest Additions. To resolve this, start by accessing the vob using ssh:
```
vagrant ssh
[vagrant@lamp ~]$
```
Then execute the command `sudo yum install kernel-devel`:
```
[vagrant@lamp ~]$ sudo yum install kernel-devel
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.coreix.net
 * extras: anorien.csc.warwick.ac.uk
 * updates: mirrors.coreix.net
Resolving Dependencies
--> Running transaction check
---> Package kernel-devel.x86_64 0:3.10.0-862.2.3.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

===================================================================================================================
 Package                     Arch                  Version                            Repository              Size
===================================================================================================================
Installing:
 kernel-devel                x86_64                3.10.0-862.2.3.el7                 updates                 16 M

Transaction Summary
===================================================================================================================
Install  1 Package

Total download size: 16 M
Installed size: 37 M
Downloading packages:
updates/7/x86_64/prestodelta                                                                |  57 kB  00:00:00     
kernel-devel-3.10.0-862.2.3.el7.x86_64.rpm                                                  |  16 MB  00:00:03     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : kernel-devel-3.10.0-862.2.3.el7.x86_64                                                          1/1
  Verifying  : kernel-devel-3.10.0-862.2.3.el7.x86_64                                                          1/1

Installed:
  kernel-devel.x86_64 0:3.10.0-862.2.3.el7                                                                         

Complete!
```
Once that is complete, type `exit` to leave the ssh session.

## 4. Add synced folder and forwarded ports
Next, we're going to add a synchronised, shared folder to the Vagrantfile and forward some of the ports that we're going to use later. Add these lines to the Vagrantfile - they should go before the final `end` statement:
```
  config.vm.network :forwarded_port, guest: 80, host: 8080
  config.vm.network :forwarded_port, guest: 3306, host: 3306
  config.vm.synced_folder '.', '/var/www/vagrant', mount_options: ['dmode=0775', 'fmode=0664']
```
Your complete Vagrantfile should look like this:
```
# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = '2'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/xenial64"
  # Set name
  config.vm.hostname = 'lamp'
  config.vm.define :lamp  do |lamp|
  end
  config.vm.provider :virtualbox do |vb|
    vb.name = 'lamp'
  end
  config.vm.network :forwarded_port, guest: 80, host: 8080
  config.vm.network :forwarded_port, guest: 3306, host: 3306
  config.vm.synced_folder '.', '/var/www/vagrant', mount_options: ['dmode=0775', 'fmode=0664']
end
```
Now we need to reload these changes - we can do this with the command `vagrant reload` - this is, in effect, a `vagrant halt` followed by a `vagrant up`.

## 5. Set up inline provisioning script
Next, we're going to use some of the Ruby features that were mentioned earlier to set up an inline shell script to perform the provisioning steps that we need to set up the remaining elements of the LAMP stack.

Add this section of code after the Ruby headers, but before the `Vagrant.configure` statement:
```
# Inline provisioning script
$provisionlamp = <<-SCRIPT
apt-get update -y && apt-get upgrade -y
apt-get install -y apache2
debconf-set-selections <<< 'mysql-server mysql-server/root_password password password'
debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password password'
apt-get -y install mysql-server
apt-get -y install php7.0-mysql
apt-get -y install php7.0 libapache2-mod-php7.0
SCRIPT
```
Then add this line to the `Vagrant.configure` section, just before the final `end`:
```
  config.vm.provision "shell", inline: $provisionlamp
```

The script contains what are essentially the apt-get commands that you would need to install the packages you need, along with instructions to set the mysql password (to 'password' - so not very secure!)...

The final script should look something like this:
```
# -*- mode: ruby -*-
# vi: set ft=ruby :

# Inline provisioning script
$provisionlamp = <<-SCRIPT
apt-get update -y && apt-get upgrade -y
apt-get install -y apache2
debconf-set-selections <<< 'mysql-server mysql-server/root_password password password'
debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password password'
apt-get -y install mysql-server
apt-get -y install php7.0-mysql
apt-get -y install php7.0 libapache2-mod-php7.0
SCRIPT

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = '2'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/xenial64"
  # Set name
  config.vm.hostname = 'ubntlamp'
  config.vm.define :ubntlamp  do |ubntlamp|
  end
  config.vm.provider :virtualbox do |vb|
    vb.name = 'ubntlamp'
  end
  config.vm.network :forwarded_port, guest: 80, host: 8080
  config.vm.network :forwarded_port, guest: 3306, host: 3306
  config.vm.synced_folder '.', '/var/www/vagrant', mount_options: ['dmode=0775', 'fmode=0664']
  config.vm.provision "shell", inline: $provisionlamp
end
```

## 6. Test the server!
