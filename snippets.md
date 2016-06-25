Ansible
------
```bash
ansible all -i hosts -m ping
ansible webserver -i hosts -m service -a "name=httpd enabled=no state=stopped"
ansible dbserver -i hosts -m command -a "shutdown -h now" --sudo

ansible myhost -i myinventory -m setup

ansible-playbook -i myhostfile -u --tags "mytag"

ansible-playbook -i myhostfile -u --extra-vars="var1=a var2=b" --tags "mytag" 

ansible-playbook -i myhostfile -u -e="var1=a var2=b" --step --start-at-task "my task for something"

# forced changed true for a certain variable
ansible-playbook -i myhostfile -u -e="myvar={{dict(changed='true')}}" --start-at-task "my task for something"

# pass a list
apb -i hosts foo.yml -e "mylist=[1,2]"

# peform syntax check but don't run
apb -i hosts --syntax-check myplaybook.yml

apb -i hosts --private-key=~/.vagrant.d/insecure_private_key -u vagrant playbooks/myplaybook.yml

alias apb=ansible-playbook
apb --check test.yml -u vagrant -i hosts  --private-key ~/.vagrant.d/insecure_private_key --step --start-at-task="known"

apb -i inventory/dev --private-key=~/.vagrant.d/insecure_private_key -u vagrant playbooks/provision.yml

apb -i hosts --private-key=~/.vagrant.d/insecure_private_key -u vagrant playbooks/vagrant.yml

# Specify a certain machine to run the playbook
apb -i hosts --limit vm1 --private-key=~/.vagrant.d/insecure_private_key -u vagrant playbooks/vagrant.yml
or
$ apb -limit vm1 --private-key=~/.vagrant.d/insecure_private_key -u vagrant playbooks/vagrant.yml

# playbook
vars:
  mylist:
    - hello world
    - hola mundo
tasks:

    - yum: name={{ item }} state=installed
      with_items:
         - httpd
         - memcached
      tags:
         - packages

    - template: src=templates/src.j2 dest=/etc/foo.conf
      tags:
         - configuration
    - debug:
        msg="{{items}}"
        with_items: mylist
        
# command
$ apb example.yml --tags "configuration,packages"

# run locally
apb -i "localhost," -c local playbooks/local.yml
sudo ansible-playbook -i "localhost," -c local playbooks/local.yml

quick reference: https://github.com/lorin/ansible-quickref
ansible configuration reference: http://docs.ansible.com/intro_configuration.html
```

Ansible user module
------------
```yaml
# usage
  #Define user and password variables
  vars:
    # created with:
    #  python -c 'import crypt; print crypt.crypt("mypassword", "myseed")
    password : myylAylKPNtmw
    user : guest
  tasks:
    - name: add user
      user name={{ user }} password={{ password }} update_password=always 
                   shell=/bin/bash home=/home/{{ user }}

```

Ansible deployment
-----------------
* `ssh-keygen -R <ip address>` - remove in known_hosts file
* `ssh -i myprivatekey.pem myuser@host` - ssh using a certain private key
* `vagrant up` - import base box & provision with ansible
* `ansible-playbook -i inventory/production provision.yml` - provision production servers
* `ansible-playbook -i inventory/staging provision.yml` - provision staging servers
* `ansible-playbook -i inventory/production deploy.yml` - deploy production servers
* `ansible-playbook -i inventory/staging deploy.yml` - deploy staging servers
* `vagrant ssh -c 'sudo service app restart'` - restart app service on vagrant machine
* `vagrant ssh -c 'tail -f /var/log/app' &` - watch stdout/stderr for index.js running on vagrant box
* `pgrep -fl 'tail -f /var/log/app' | xargs kill` - stop watching /var/log/app

ansible vagrant inventory
----------------------
```yaml
[targets]
#localhost              ansible_connection=local 
vagrant host_key_checking=False ansible_ssh_user=vagrant ansible_ssh_host=192.168.23.13 ansible_ssh_private_key_file=~/.vagrant.d/insecure_private_key 
```
