

Vagrant installation
-----------------------------------
* sudo apt-get install ruby  OR sudo apt-get install ruby-dev
* sudo apt-get install make
* sudo apt-get install gcc make
* wget -c https://releases.hashicorp.com/vagrant/2.2.4/vagrant_2.2.4_x86_64.deb
* sudo dpkg -i vagrant_2.2.4_x86_64.deb


We can install using apt also but as of now ubuntu 18.04TLS does not have stable version.

[Ignore] apt commands to install 
-----------------------
```
sudo apt update
sudo apt install vagrant
vagrant plugin install vagrant-aws
```


Check version
-------------
```
vagrant -v
```

Install AWS plugin
-------------------
```
vagrant plugin install vagrant-aws
```

Fetch a Vagrant box image
---------------------------
```
vagrant box add dummy https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box
```

Configure Vagrant file
-----------------------
Make a directory to hold your Vagrant machine metadata.
Run the following commands:
```
mkdir aws-demo
cd aws-demo
vagrant init
```

Find Access-Key and Secret-Access-Key
--------------------------------------
* login to -> https://console.aws.amazon.com/iam/home?#/home
* go to users--> select user --> Security Credentials --> Create Access Key

Edit Vagrantfile
-------------------
```
# -*- mode: ruby -*-
# vi: set ft=ruby :
require 'vagrant-aws'
Vagrant.configure('2') do |config|
    config.vm.box = 'dummy'
    config.vm.provider 'aws' do |aws, override|
	config.vm.network "private_network", type: "dhcp"
    config.vm.synced_folder ".", "/tmp", type: "rsync" #after configuring this we were able to see file and shell provisioner
    aws.access_key_id = "XXXXXXXXXXXXXXXXXXXX"
    aws.secret_access_key = "XXXXXXXXXXXXXX"
    aws.keypair_name = 'keypair name'
    aws.instance_type = "t2.micro"
    aws.region = 'us-east-2'
    aws.availability_zone = "us-east-2c"
    aws.ami = 'ami-0d5d9d301c853a04a' #'ami-7747d01e'
    aws.security_groups =["sg-0351458c629c2e8d0"] # Give your security Group ID
    aws.subnet_id = "subnet-d4ed6f98"  # find aws.region whereever you want to create VMs
    override.ssh.username = "ubuntu"
    override.ssh.private_key_path = "/home/ubuntu/aianalytics.pem"
  end
  config.vm.provision "file", source: "/home/ubuntu/file_to_copy.sh", destination: "/tmp/file_to_copy.sh"
  config.vm.provision "shell",
    inline: "cp /tmp/file_to_copy.sh /home/ubuntu/file_to_copy.sh"
  # Configuration for Ansible as Provisioner
  #config.vm.provision :ansible do |ansible|
   # ansible.playbook = "site.yml"
   # ansible.verbose = "v"
   #  ansible.host_key_checking = false
   #  ansible.limit = 'all'
  #end
end

```

Launch the instance that is configured by the Vagrant file by running the following command
-------------------------------------------------------------------------------------------
```
vagrant up --provider=aws
vagrant up --provider=aws --debug
```

Login to the AWS EC2 instance (Newly created by Vagrant)
------------------------------
```
vagrant ssh
```

vagrant ssh <host_name> #in case of multi machine we can use vagrant ssh web or vargant ssh db

The above setup will successfully provision a t2.micro EC2 machine via vagrant

Reload Provision
--------------------
```
vagrant reload --provision
vagrant reload --provision --debug
```

Vagrant file to create multiple machine
-----------------------------------------
https://github.com/hashicorp/consul/blob/master/demo/vagrant-cluster/Vagrantfile


To uninstall only Vagrant
------------------------------
```
sudo apt-get remove vagrant

sudo apt-get remove vagrant
```

To uninstall Vagrant and its dependencies
------------------------------------------
```
 sudo apt-get remove --auto-remove vagrant

 sudo apt-get remove --auto-remove vagrant
 ```
Using dpkg package manager
---------------------------------
This way is used for those who installed Vagrant by downloading the Vagrant package from its site and install manually using dpkg, a package manager for Debian-based systems.

To uninstall Vagrant on Ubuntu, we simply need to execute the below command:
```
sudo dpkg -r vagrant

sudo dpkg -r vagrant
```
Incase you are facing below issue then install vagrant-vbguest plugin
----------------------------------------------------------------------
```
[ERROR]
No host IP was given to the Vagrant core NFS helper. This is an internal error that should be reported as a bug.
```
```
vagrant plugin install vagrant-vbguest
config.vm.network "private_network", type: "dhcp"
```
