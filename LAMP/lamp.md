### Lamp stack


1. Used Vagrant file and provisioners from this [source](https://github.com/iambryancs/vagrant-ansible-lamp)
1. Readme in the project repo is self-explanatory and worked straight on my dev laptop, and should be good enough for the demo.   
1. If we are happy, then we can just do this for lamp.

### TODO
1. Need to tweak few ansible bits, and bootstrap steps to validate VirtualBox Guest Additions, vagrant sharefolders etc. work  
1. Show how we can use the same provisioning and up the vagrant box in aws etc.
1. vagrant plugin install vagrant-vbguest . Run this to work around VirtualBox Guest Additions

### Useful Links
1. [Getting Started](https://www.vagrantup.com/intro/getting-started/index.html)
1. [List of Vagrant boxes] (https://app.vagrantup.com/boxes/search?provider=virtualbox)
1. [LAMP source](https://github.com/iambryancs/vagrant-ansible-lamp)
1. [Vagrant vs Docker] (https://www.vagrantup.com/intro/vs/docker.html)



### Need to get everything required available on USB:
Sequence from iambryancs repo is:
1. Need to be able to yum install the following (local rpms?)
  * yum install -y kernel-devel-\`uname -r\` gcc binutils make perl bzip2
  * This is part of the standard Vagrant up process - need to find out about vagrant up run offline
  * Still haven't worked out why this is happening - create box with all this and guest additions in place?
  * What about plugins offline?
