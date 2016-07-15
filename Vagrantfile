VAGRANTFILE_API_VERSION = "2"

base_dir = File.expand_path(File.dirname(__FILE__))
cluster = {
  "node1" => { :ip => "172.168.1.11",  :cpus => 1, :mem => 768 },
  "node2" => { :ip => "172.168.1.12",  :cpus => 1, :mem => 768 },
  "node3" => { :ip => "172.168.1.13",  :cpus => 1, :mem => 768 },
}

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box #:machine
  end # end if

  # manage /etc/hosts on boxes and host
  # ( requires vagrant-hostmanager plugin)
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.include_offline = true

  cluster.each_with_index do |(hostname, info), index|
    config.vm.define hostname do |cfg|

      cfg.vm.provider :virtualbox do |vb, override|
        override.vm.box = "CentOS7_x64"
        override.vm.box_url = "https://github.com/holms/vagrant-centos7-box/releases/download/7.1.1503.001/CentOS-7.1.1503-x86_64-netboot.box"
        override.vm.network :private_network, ip: "#{info[:ip]}", virtualbox_inet:"test_cluster"
        override.vm.hostname = hostname

        vb.name = 'vagrant-' + hostname
        vb.customize ["modifyvm", :id, "--memory", info[:mem], "--cpus", info[:cpus], "--hwvirtex", "on", "--ioapic", "on" ]
      end # end cfg.vm.provider

      # provision nodes with ansible
      if index == cluster.size - 1
        cfg.vm.provision :ansible do |ansible|
          ansible.verbose = "v"
          # ansible.sudo = true
          # ansible.inventory_path = "inventory/vagrant"
          ansible.playbook = "cluster.yml"
          ansible.limit = 'all'# "#{info[:ip]}" # Ansible hosts are identified by ip
        end # end cfg.vm.provision
      end #end if

    end # end config

  end #end cluster

end #end vagrant
