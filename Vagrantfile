# -*- mode: ruby -*-
# vi: set ft=ruby :

# NOTE: vagrant-libvirt needs to run in series (not in parallel) to avoid
# trying to create the network twice... eg: vagrant up --no-parallel
# alternatively, you can just create the vm's one at a time manually...

domain = 'local'
box = 'peru/windows-10-enterprise-x64-eval'
cpus = 2
ram = 1024

node_components = [
  {:hostname => 'vm0',  :ip => '172.16.32.10', :box => box, :fwdhost => 2222, :fwdguest => 22, :cpus => cpus, :ram => ram},
  # {:hostname => 'vm1', :ip => '172.16.32.11', :box => box},
  # {:hostname => 'vm2', :ip => '172.16.32.12', :box => box},
]

Vagrant.configure("2") do |config|
  node_components.each do |node|
    config.vm.define node[:hostname] do |node_config|
      node_config.ssh.username = 'vagrant'
      node_config.ssh.password = 'vagrant'
      node_config.ssh.insert_key = true

      node_config.vm.box = node[:box]
      node_config.vm.hostname = node[:hostname] + '.' + domain

      node_config.vm.network :private_network,
                             ip: node[:ip],
                             :autostart => true,
                             libvirt__forward_mode: 'nat',
                             libvirt__dhcp_enabled: true
      if node[:fwdhost]
        node_config.vm.network :forwarded_port, guest: node[:fwdguest], host: node[:fwdhost]
      end


      memory = node[:ram] ? node[:ram] : 256;
      cpus = node[:cpus] ? node[:cpus] : 4;
      node_config.vm.provider :libvirt do |libvirt, override|
        libvirt.driver = "kvm"

        # comment out host to connect directly with qemu:///system
        # libvirt.host = "localhost"

        # If use ssh tunnel to connect to Libvirt.
        libvirt.connect_via_ssh = false

        # The username and password to access Libvirt. Password is not used when
        # connecting via ssh.
        libvirt.username = "root"

        # Libvirt storage pool name
        libvirt.storage_pool_name = "default"

        # System configuration
        libvirt.cpus = cpus.to_s
        libvirt.memory = memory.to_i
        libvirt.nested = true

        # Only available with KVM
        libvirt.cpu_mode = "host-passthrough"
      end

    end
  end
end
