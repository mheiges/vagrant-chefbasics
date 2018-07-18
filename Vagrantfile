TLD = 'chef.vm'


CHEF_NODES = {
  :server => {
    :vagrant_box     => 'bento/centos-7',
    :wf_hostname     => 'server.' + TLD,
    :playbook        => 'ansible/chef_server.yml'
  },
  :node_centos => {
    :vagrant_box     => 'bento/centos-7',
    :wf_hostname     => 'node-centos.' + TLD,
    :playbook        => 'ansible/chef_node.yml'
  },
  :node_ubuntu => {
    :vagrant_box     => 'bento/ubuntu-14.04',
    :wf_hostname     => 'node-ubuntu.' + TLD,
    :playbook        => 'ansible/chef_node.yml'
  },
}


Vagrant.configure(2) do |config|

  CHEF_NODES.each do |name,cfg|
    config.vm.define name do |vm_config|

      vm_config.vm.provider 'virtualbox' do |v|
        v.gui = false
      end

      if Vagrant.has_plugin?('landrush')
        vm_config.landrush.enabled = true
        vm_config.landrush.tld = TLD
      end

      vm_config.vm.box      = cfg[:vagrant_box]     if cfg[:vagrant_box]
      vm_config.vm.hostname = cfg[:wf_hostname]     if cfg[:wf_hostname]

      vm_config.ssh.forward_agent = true
      vm_config.ssh.pty = true

      vm_config.vm.provision 'ansible' do |ansible|
        ansible.playbook = cfg[:playbook]
        ansible.compatibility_mode = '2.0'
      end

    end
  end


end