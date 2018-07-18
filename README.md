
Vagrant box for Chef tutorials, experimentation.

https://learn.chef.io/modules#/

# SSL Certificate

A self-signed certificate and key are installed from `ansible/files/ssl`
so a consistent cert is presented to web browsers for exclusion (for
Management Console access). This is especially useful with Safari which
caches excluded certs and will refuse access to the Console if the
excluded cert has changed (i.e. the VM is recreated).

The cert was generated with,

````
openssl req -x509 -newkey rsa:4096 \
  -keyout server.chef.vm.key \
  -out server.chef.vm.crt \
  -days 3650 -nodes -subj '/CN=server.chef.vm'
````

# Setup

### Chef server only (CentOS)

```
vagrant up server
```

This installs a standalone Chef server following https://docs.chef.io/install_server.html

The management console is at https://server.chef.vm with
username/password `admin`/`vagrant`. See `ansible/config.yml` for
current settings, including `chef_management_console_install` to disable
installing the console.


### Node only (CentOS)

```
vagrant up node_centos
```

### Node only (Ubuntu)

```
vagrant up node_ubuntu
```

### Both server and node

```
vagrant up
```

## `workstation`

For Mac OS X, download, install package from https://downloads.chef.io/chefdk#mac_os_x

Following https://learn.chef.io/modules/manage-a-node-chef-server/rhel/bring-your-own-system/set-up-your-chef-server#/

The Vagrant `server` node provisioning created a private key and a
`knife.rb` file that you can copy into your `.chef` directory as (or use
symlinks as shown). The `knife.rb` file should be set with correct
values based on the Ansible `ansible/config.yml` used during the vagrant
provision, but review it to be sure. The `pem` file will be recreated if
`chef_server_recreate_existing_users` is true in `ansible/config.yml`
during a vagrant re-provision, so you will need to update the file in
the `.chef` directory and again run `knife ssl fetch`.

````
mkdir -p ~/learn-chef/.chef
ln ~/Vagrant/vagrant-chefbasics/scratch/chef_admin.pem ~/learn-chef/.chef/
ln ~/Vagrant/vagrant-chefbasics/scratch/knife.rb ~/learn-chef/.chef/
````


### Quick Start


Work within the `~/learn-chef` directory (where the `.chef` dir is located)

```
cd ~/learn-chef
````


The Vagrant `server` node provisioning created a private key and a
`knife.rb` file that you can copy into your `.chef` directory as (or use
symlinks as shown). The `knife.rb` file should be set with correct
values based on the Ansible `ansible/config.yml` used during the vagrant
provision, but review it to be sure. The `pem` file will be recreated if
`chef_server_recreate_existing_users` is true in `ansible/config.yml`
during a vagrant re-provision, so you will need to update the file in
the `.chef` directory and again run `knife ssl fetch`.

````
mkdir -p ~/learn-chef/.chef
ln -nfs ~/Vagrant/vagrant-chefbasics/scratch/chef_admin.pem ~/learn-chef/.chef/
ln -nfs ~/Vagrant/vagrant-chefbasics/scratch/knife.rb ~/learn-chef/.chef/
````


````
knife ssl fetch
````

```
knife ssl check
````


````
knife cookbook upload learn_chef_httpd
knife cookbook list
````

Bootstrap and run recipe

````
knife bootstrap node.chef.vm \
  --ssh-user vagrant \
  --sudo \
  --identity-file ~/Vagrant/vagrant-chefbasics/.vagrant/machines/node_centos/virtualbox/private_key \
  --node-name node_centos \
  --run-list 'recipe[learn_chef_httpd]'
````

knife node list
knife node show node_centos

knife ssh node.chef.vm   'sudo chef-client'   --ssh-user vagrant   --ssh-identity-file ~/Vagrant/vagrant-chefbasics/.vagrant/machines/node_centos/virtualbox/private_key   --manual-list


Install from Berksfile specs,

````
berks install
````

````
berks upload --no-ssl-verify
````


knife role from file roles/web.json

knife role list

knife role show web

knife node run_list set node_centos "role[web]"

knife node show node_centos --run-list

knife ssh 'role:web'   'sudo chef-client'   --ssh-user vagrant   --ssh-identity-file ~/Vagrant/vagrant-chefbasics/.vagrant/machines/node_centos/virtualbox/private_key

knife status 'role:web' --run-list

Delete a node

```
knife node delete node1 --yes
knife client delete node1 --yes
```


Delete a cookbook (all versions) from server

````
knife cookbook delete learn_chef_httpd --all --yes
````

Delete a role from server

````
knife role delete web --yes
````

## `server`

### Install Chef Server

Chef server is installed to the `server` node by Ansible following
https://learn.chef.io/modules/manage-a-node-chef-server/rhel/bring-your-own-system/set-up-your-chef-server#/


## Bootstrap Chef `node`

On Chef workstation (OS X), run

````
knife bootstrap node.chef.vm \
  --ssh-user vagrant \
  --sudo \
  --ssh-identity-file ~/Vagrant/vagrant-chefbasics/.vagrant/machines/node/virtualbox/private_key \
  --node-name node1 --run-list 'recipe[learn_chef_httpd]'
````

# Update Chef `node`

knife ssh node.chef.vm \
  'sudo chef-client' \
  --ssh-user vagrant \
  --ssh-identity-file ~/Vagrant/vagrant-chefbasics/.vagrant/machines/node/virtualbox/private_key \
  --manual-list






# Kitchen


kitchen list

kitchen exec -c whoami

kitchen login

kitchen test
  kitchen create
  kitchen converge
  kitchen verify



chef generate cookbook cookbooks/lamp
chef generate attribute cookbooks/lamp default
chef generate template default.conf
chef generate recipe web
chef generate file create-tables.sql
chef generate template index.php

Create 'passwords' data bag
knife data bag create passwords
Update data bag
knife data bag from file passwords mysql.json

knife data bag show passwords mysql

rm Berksfile.lock ??
berks install

berks upload --no-ssl-verify

knife bootstrap node-ubuntu.chef.vm \
  --ssh-user vagrant\
  --sudo \
  --identity-file ~/Vagrant/vagrant-chefbasics/.vagrant/machines/node_ubuntu/virtualbox/private_key \
  --node-name node-ubuntu \
  --run-list 'recipe[lamp_customers]'



knife ssh node-ubuntu.chef.vm \
  'sudo chef-client' \
  --ssh-user vagrant \
  --ssh-identity-file ~/Vagrant/vagrant-chefbasics/.vagrant/machines/node_ubuntu/virtualbox/private_key \
  --manual-list



Get cookbook versions
knife cookbook show lamp_customers
Show selected recipe
knife cookbook show lamp_customers 0.1.5  recipes default.rb

