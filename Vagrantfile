# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Boxes at https://vagrantcloud.com/search.
  config.vm.box = ""

  boxes = [
    { 
      :name => "forensic-lab",
      :box => "gusztavvargadr/windows-11-22h2-enterprise",
      :ip => "192.168.56.110",
      :shared_folder => "./forensic",
      :memory => 6144
    },
    { 
      :name => "reverse-lab",
      :box => "gusztavvargadr/windows-11-22h2-enterprise",
      :ip => "192.168.56.120",
      :shared_folder => "./reverse",
      :memory => 4096
    },
    { 
      :name => "maldev-lab",
      :box => "gusztavvargadr/windows-11-22h2-enterprise",
      :ip => "192.168.56.130",
      :shared_folder => "./maldev",
      :memory => 6144
    }
  ]

  config.vm.provider "virtualbox" do |vb|
    vb.gui = true
    vb.memory = 4096
    vb.cpus = 2
    vb.customize ["modifyvm", :id, "--groups", "/Lab"]
    vb.customize ["modifyvm", :id, "--clipboard-mode", "bidirectional"]
  end

  boxes.each do |box|
    config.vm.define box[:name] do |target|
      # Boxes at https://vagrantcloud.com/search.
      target.vm.box = box[:box]
      target.vm.hostname = box[:name]

      target.vm.network "private_network", ip: box[:ip]
      target.vm.synced_folder box[:shared_folder], "/vagrant", automount: true

      target.vm.provider "virtualbox" do |vb|
        vb.name = box[:name]
        vb.memory = box[:memory]
      end

      target.vm.guest = :windows
      target.vm.communicator = "winrm"
      target.winrm.basic_auth_only = true
      target.winrm.transport = :plaintext

      target.vm.provision :shell, :path => "vagrant/Install-WMF3Hotfix.ps1", privileged: false
      target.vm.provision :shell, :path => "vagrant/ConfigureRemotingForAnsible.ps1", privileged: false
      target.vm.provision "ansible" do |ansible|
        ansible.playbook = "playbook/install.yml"
        ansible.inventory_path = "playbook/inventory.ini"
        ansible.verbose = true
        # ansible.limit = box[:name]
        ansible.host_vars = {
          box[:name] => { "ansible_winrm_scheme" => "http" },
        }
      end
    end
  end
end