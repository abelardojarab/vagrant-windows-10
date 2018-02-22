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
  {:hostname => 'vm0',  :ip => '192.168.122.10', :box => box, :fwdhost => 4422, :fwdguest => 22, :cpus => cpus, :ram => ram},
  # {:hostname => 'vm1', :ip => '192.168.122.11', :box => box},
  # {:hostname => 'vm2', :ip => '192.168.122.12', :box => box},
]

Vagrant.configure("2") do |config|
  node_components.each do |node|
    config.vm.define node[:hostname] do |node_config|
      node_config.vm.boot_timeout = 500

      node_config.ssh.username = 'vagrant'
      node_config.ssh.password = 'vagrant'
      node_config.ssh.insert_key = false
      # If true, then any SSH connections made will enable agent forwarding.
      # Default value: false
      node_config.ssh.forward_agent = true

      node_config.vm.box = node[:box]
      node_config.vm.hostname = node[:hostname]
      node_config.vm.communicator = "winrm"
      node_config.vm.guest = :windows
      node_config.vm.network :private_network,
                             :autostart => true,
                             ip: node[:ip],
                             libvirt__network_name: "default",
                             libvirt__forward_mode: 'nat',
                             libvirt__dhcp_enabled: true

      if node[:fwdhost]
        node_config.vm.network :forwarded_port, guest: node[:fwdguest], host: node[:fwdhost]
      end

      node_config.vm.synced_folder ".", "/vagrant"

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
