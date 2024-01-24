# ansible-examples
ansible example
This is my first ansible examples

add the key-file and inventory details in the ansible.cfg and then run just
ansible all -m ping

To list the hosts from the inventory
ansible all --list-hosts


to gather facts of all the hosts
ansible all -m gather_facts

to gather facts that limits to single host
ansible all -m gather_facts --limit 10.0.2.4


