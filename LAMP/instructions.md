# Creating a LAMP Stack using ansible

## Setup

You should already have VirtualBox and Vagrant installed on your machine. If you don't, please follow these instructions:

### Windows:
#### VirtualBox
Double-click on the 'VirtualBox-5.2.12-122591-Win.exe' installer and follow the instructions.
#### Vagrant
Double-click on the 'vagrant_2.1.1_x86_64.msi' installer and follow the instructions.
### Mac:
#### VirtualBox
Double-click on "VirtualBox-5.2.12-122591-OSX.dmg" to install VirtualBox

When the installers verifies and starts up, double-click on the VirtualBox.pkg icon shown in Step 1

Hit continue and agree to the terms, you should then get an "Installation Successful" message - click to close and you're done. 

You can now start the VirtualBox application from inside your Applications Folder.

#### Vagrant
Double-click on the vagrant_2.1.1_x86_64.dmg file to start the installation

When the installer starts up, double-click on the vagrant.pkg icon

Click continue then install, authorise if required, then click close

Open a terminal and type "vagrant" to see usage instructions

### Linux:
#### VirtualBox
You should be able to find the package that suits your OS on the USB stick at /VBox/Linux.

Install using e.g.: `sudo yum install -y <file>.rpm` or `sudo dpkg -i <file>.deb`
#### Vagrant
You should be able to find the package that suits your OS on the USB stick at /Vagrant/Linux.

Install using e.g.: `sudo yum install -y <file>.rpm` or `sudo dpkg -i <file>.deb`
## Confirm Vagrant
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
  config.vm.define :lamp  do |lamp|
    lamp.vm.box = "ubuntu/xenial64"
    # Set name
    lamp.vm.hostname = 'lamp'
    lamp.vm.provider :virtualbox do |vb|
      vb.name = 'lamp'
    end
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
There is a lot more output as Vagrant installs some of the required packages and the VirtualBox Guest Additions.

## 4. Add synced folder and forwarded ports
Next, we're going to add a synchronised, shared folder to the Vagrantfile and forward some of the ports that we're going to use later. Add these lines to the Vagrantfile - they should go just before the final two `end` statements:
```
  lamp.vm.network :forwarded_port, guest: 80, host: 8080
  lamp.vm.network :forwarded_port, guest: 3306, host: 3306
  lamp.vm.synced_folder '.', '/var/www/vagrant', mount_options: ['dmode=0775', 'fmode=0664']
```
Your complete Vagrantfile should look like this:
```
# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = '2'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define :lamp  do |lamp|
    lamp.vm.box = "ubuntu/xenial64"
    # Set name
    lamp.vm.hostname = 'lamp'
    lamp.vm.provider :virtualbox do |vb|
      vb.name = 'lamp'
    end
    lamp.vm.network :forwarded_port, guest: 80, host: 8080
    lamp.vm.network :forwarded_port, guest: 3306, host: 3306
    lamp.vm.synced_folder '.', '/var/www/vagrant', mount_options: ['dmode=0775', 'fmode=0664']
  end
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
Then add this line to the `Vagrant.configure` section, just before the final two `end` statements:
```
  lamp.vm.provision "shell", inline: $provisionlamp
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
  config.vm.define :lamp  do |lamp|
    lamp.vm.box = "ubuntu/xenial64"
    # Set name
    lamp.vm.hostname = 'lamp'
    lamp.vm.provider :virtualbox do |vb|
      vb.name = 'lamp'
    end
    lamp.vm.network :forwarded_port, guest: 80, host: 8080
    lamp.vm.network :forwarded_port, guest: 3306, host: 3306
    lamp.vm.synced_folder '.', '/var/www/vagrant', mount_options: ['dmode=0775', 'fmode=0664']
    lamp.vm.provision "shell", inline: $provisionlamp
  end
end
```
You'll need to reload the configuration again, but, by default, the `vagrant reload` command does not reload the configuration - so, you'll need to force that by running:
```
vagrant reload --configuration
```
You should see the VM performing the installation of all the packages you requested in the inline script.

## 6. Test the server!
