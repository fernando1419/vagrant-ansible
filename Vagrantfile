Vagrant.configure("2") do |config|
  # VM NODE1 - Web Server1
  config.vm.define "node1" do |node1|
    node1.vm.box = "ubuntu/jammy64"
    node1.vm.hostname = "node1"
    node1.vm.network "private_network", ip: "192.168.50.100"
    node1.vm.synced_folder ".", "/vagrant", disabled: true

    node1.vm.provider "virtualbox" do |vb|
      vb.name = "day6-challenge-Node1_vagrant-vm"
      vb.memory = 512
      vb.cpus = 1
    end
  end

  # VM NODE2 - Web Server2
  config.vm.define "node2" do |node2|
    node2.vm.box = "ubuntu/focal64"
    node2.vm.hostname = "node2"
    node2.vm.network "private_network", ip: "192.168.50.200"
    node2.vm.synced_folder ".", "/vagrant", disabled: true

    node2.vm.provider "virtualbox" do |vb|
      vb.name = "day6-challenge-Node2_vagrant-vm"
      vb.memory = 512
      vb.cpus = 1
    end

  end

  # VM Ansible Server - Control Node
  config.vm.define "ansible" do |ansible|
    ansible.vm.box = "generic/rocky9"
    ansible.vm.hostname = "ansible"
    # config.vm.network "forwarded_port", guest: 443, host: 8443
    # ansible.vm.network "forwarded_port", guest: 80, host: 8080
    ansible.vm.network "private_network", ip: "192.168.50.1"
    ansible.vm.synced_folder ".", "/vagrant"

    ansible.vm.provider "virtualbox" do |vb|
      vb.name = "day6-challenge-AnsibleControlNode_vagrant-vm"
      vb.gui = false
      vb.memory = 1024
      vb.cpus = 1
    end

    ansible.vm.provision "shell", inline: <<-SHELL
      sudo dnf install -y epel-release ansible
      mkdir -p /home/vagrant/.ssh
      cp /vagrant/.vagrant/machines/node1/virtualbox/private_key /home/vagrant/.ssh/node1_key
      cp /vagrant/.vagrant/machines/node2/virtualbox/private_key /home/vagrant/.ssh/node2_key
      chmod 600 /home/vagrant/.ssh/node1_key
      chmod 600 /home/vagrant/.ssh/node2_key
      chown vagrant:vagrant /home/vagrant/.ssh/node1_key /home/vagrant/.ssh/node2_key
    SHELL
    
  end

end
