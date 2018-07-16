
Vagrant box for Chef tutorials, experimentation.

https://learn.chef.io/modules#/

# Setup

Chef server only (CentOS)

```
vagrant up server
```

This installs a standalone Chef server following https://docs.chef.io/install_server.html

Node only (CentOS)

```
vagrant up node_centos
```

Node only (Ubuntu)

```
vagrant up node_ubuntu
```

Both server and node

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
