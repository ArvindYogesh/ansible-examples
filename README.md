Vm-03 workstation = 10.0.2.15
Server-01 Ubuntu = 10.0.2.4
Server-02 Ubuntu = 10.0.2.5
Server-03 CentOS = 10.0.2.6


sudo -



ip a
ls -la .ssh
ssh-keygen -t ed25519 -c “vbox default”
ssh-copy-id -I Ip a
ls -la .ssh
ssh-keygen -t ed25519 -c “vbox default”

copy ssh to another server
ssh-copy-id -i ~/.ssh/abc.pub $server_address

add ssh-key to the ssh-agent
- eval $(ssh-agent)
- check the process running in the background: ps aux | grep 2362
////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
Server-03 = 10.0.2.6- CentOS
to add user to to sudoer
- type "visudo" - it open /etc/sudoers
- go to the last and type "username ALL=(ALL) NOPASSWD:ALL"


1. 
sudo yum –y install openssh-server

2. apache doesn't work in the centos on the first go, do 2 steps
a.
$systemctl status httpd 
check it would be disabled 

to enable, enter
sudo systemctl start httpd

b. 
sudo firewall-cmd --add-port=80/tcp


////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

check using ping - that performs connection to the host- ansible can connect to each of the server and establish a connection.
$ ansible all --key-file ~/.ssh/ansible -i inventory -m ping


add the key-file and inventory details in the ansible.cfg and then run just
$ ansible all -m ping

To list the hosts from the inventory
$ ansible all --list-hosts


to gather facts of all the hosts
$ ansible all -m gather_facts

to gather facts that limits to single host
$ ansible all -m gather_facts --limit 10.0.2.4

to gather facts that limits to single host and grep the facts from it
$ ansible all -m gather_facts --limit 10.0.2.6 | grep ansible_distribution

///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

1. 
how to remove sudo as apt module fails without sudo. EG
$ ansible all -m apt -a update_cache=true

solution: add "--become", "--ask-become-pass"
$ ansible all -m apt -a update_cache=true --become --ask-become-pass

become - elevate the privileges - here it is sudo passwrod. There are also other ways, just check it out


2. install package to all hosts in one command
$ ansible all -m apt -a name=vim-nox --become --ask-become-pass
$ ansible all -m apt -a name=tmux --become --ask-become-pass
$ ansible all -m apt -a "name=snapd state=latest" --become --ask-become-pass

to upgrade all the packages across the hosts
$ ansible all -m apt -a "upgrade=dist" become --ask-become-pass


///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

1. To remove the package from the hosts using playbook
 "state: absent"

2. When condition

//
when: ansible_distribution == "Ubuntu"
when: ansible_distribution in ["Debian", "Ubuntu"]
when: ansible_distribution in ["Debian", "Ubuntu"] and ansible_distribution_version == "8.2"


3. Update the inventory with variables to pass

inventory for install-apache-v3.yml
10.0.2.4 apache_package=apache2 php_package=libapache2-mod-php
10.0.2.5 apache_package=apache2 php_package=libapache2-mod-php
10.0.2.6 apache_package=httpd php_package=php

in the playbook, the variable should be defined in double_quotes "{{ apache_package }}"

4. inventory for site.yml [targeting specific nodes]

[web_servers]
10.0.2.4 
10.0.2.6

[db_servers]
10.0.2.5 
10.0.2.6 

[file_servers]
10.0.2.5 
10.0.2.6 


5. tags

$ ansible-playbook --list-tags site.yml
$ ansible-playbook --tags centos --ask-become-pass site.yml
$ ansible-playbook --tags "centos,db" --ask-become-pass site.yml

6. copy files to the target host
 
 - put the file "abc.html "under "files/abc.html" 
 - you don't need to mention the folder "files" in the playbook as it is already assumed.
   - just put the file name "abc.html"

 - src file name and dest filename cannot be the same
   src: default_site.html
   dest: /var/www/html/index.html

   - here index.html => default_site.html

7. manage services
  
  - change a line in file apache config on centOS : /etc/httpd/conf/httpd.conf
  - "register: htttpd" - it caottures the state iin a variable called "httpd"

8. add user
  "authorised_key" module specifically used to create ssh for the user.


9. Roles
   - Better split up the task in much more sense


10. Host variable
- create host varible files for the IP addresses in the inventory.
- file name can be the IP addresses, host name and the dns name.

11. Handlers comes in place when any of the play or tasks "notify" changed.

12. Templates: 

 - Templates has variables in it that we can use to apply on multiple hosts and then we can independently change variables on each host.
 - You'll manage the services and config files


 - scp command securely copy the files from centos- Eg
   $ scp 10.0.2.6:/etc/ssh/sshd_config sshd_config_centos

   if the permission is denied - go to centos and execute "sudo chmod a+r /etc/ssh/sshd_config"

