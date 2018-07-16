
Vagrant box for Chef tutorials, experimentation.

https://learn.chef.io/modules#/

# Setup

Chef server only

```
vagrant up server
```

Node only

```
vagrant up node
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

Work within the `~/learn-chef` directory (where the `.chef` dir is located)

```
cd ~/learn-chef
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
  --node-name node1-centos --run-list 'recipe[learn_chef_httpd]'
````

# Update Chef `node`

knife ssh node.chef.vm \
  'sudo chef-client' \
  --ssh-user vagrant \
  --ssh-identity-file ~/Vagrant/vagrant-chefbasics/.vagrant/machines/node/virtualbox/private_key \
  --manual-list
