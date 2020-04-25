How SSH Works
----------------
- Client host initiates SSH call to remote
- Any commands that you type into your local terminal are sent through an encrypted SSH tunnel and executed on your server.
- To authenticate using SSH keys, a user must have an SSH key pair on their local computer. 
- On the remote server, the public key must be copied to a file within the user’s home directory at ~/.ssh/authorized_keys.
- When a client connects to the host, wishing to use SSH key authentication, it will inform the server of this intent and will tell the server which public key to use.
- The server then check its authorized_keys file for the public key, generate a random string and encrypts it using the public key. 

1) Create ssh key
2) Copy pub key to authorized_keys

Generating and Working with SSH Keys
------------------------------------  
ssh-keygen  
```
Generating public/private rsa key pair.  
Enter file in which to save the key (/home/username/.ssh/id_rsa):  
it will ask for passphrase : ansible 
```
cat ~/.ssh/id_rsa.pub  

Copying your Public Key Using SSH  
---------------------------------  
```
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys  # for localhost
cat id_rsa.pub >> ~/.ssh/authorized_keys  
```

Install Ansible  
----------------  
```
sudo apt-add-repository -y ppa:ansible/ansible  
sudo apt-get update 
sudo apt-get install -y ansible  
```

```
sudo mv /etc/ansible/hosts /etc/ansible/hosts.orig  
```
  
Create hosts file inside /etc/ansible/hosts and add below content 
``` 
[local]  
127.0.0.1  
```
  
ansible all -m ping  



  
Ansible setup module command  (Adhoc commonds/Tasks)
-----------------------------  
#### Display facts from all hosts and store them indexed by I(hostname) at C(/tmp/facts).  
ansible all -m setup --tree /tmp/facts  
#### Display only facts regarding memory found by ansible on all hosts and output them.  
 ansible all -m setup -a 'filter=ansible_*_mb'  
   
#### Display only facts returned by facter.  
 ansible all -m setup -a 'filter=facter_*'  
   
#### Collect only facts returned by facter.  
 ansible all -m setup -a 'gather_subset=!all,!any,facter'  
   
#### Display only facts about certain interfaces.  
 ansible all -m setup -a 'filter=ansible_eth[0-2]'  
 
ansible all -m copy -a "src=/etc/hosts dest=/tmp/hosts"

Ansible Playbooks
------------------
```
Simply put, playbooks are the basis for a really simple configuration management and multi-machine deployment system, unlike any that already exist, and one that is very well suited to deploying complex applications.

Playbooks can declare configurations, but they can also orchestrate steps of any manual ordered process, even as different steps must bounce back and forth between sets of machines in particular orders. They can launch tasks synchronously or asynchronously.

```

```
ansible-playbook echo.yml
ansible-playbook copy_test.yml
ansible-playbook nginx_playbook.yml
ansible-playbook install_apache.yml
ansible-playbook -i localhost ec2_instance.yml
```

Running Ansible as Provisioner via Vagrant
-------------------------------------------
* Add Below provisioner line to your Vagrant File
```
   config.vm.provision "ansible" do |ansible|
    ansible.playbook = "../ansible_test/copy_test.yml"
  end

  Run Command to apply Ansible provision:
  vagrant reload --provision
```

Hack for Ec2
-------------
add below in /etc/ansible/hosts
localhost ansible_python_interpreter=python
```
[ERROR] : boto not found

sudo apt -y install python-pip
pip install boto --user

```


```
[ERROR] : apt lock permission issue

https://unix.stackexchange.com/questions/385860/could-not-open-lock-file-var-lib-apt-lists-lock-open-13-permission-denied
Execute below command
sudo su
curl -sL https://deb.nodesource.com/setup_7.x | bash -
```

Ansible playbook
-------------------
https://github.com/geerlingguy/ansible-vagrant-examples

Ansible all modules
---------------------
https://docs.ansible.com/ansible/latest/modules/list_of_all_modules.html
 
Reference:  
------------   
https://docs.ansible.com/ansible/latest/modules/setup_module.html  
https://serversforhackers.com/c/an-ansible-tutorial  
