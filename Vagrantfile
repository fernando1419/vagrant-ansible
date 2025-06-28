Vagrant.configure("2") do |config|    
  # VM NODE1 - Web Server1
  config.vm.define "node1" do |node1|
    node1.vm.box = "ubuntu/jammy64"
    node1.vm.hostname = "node1"
    node1.vm.network "private_network", ip: "192.168.50.100"
    node1.vm.synced_folder ".", "/vagrant", disabled: true

    node1.vm.provider "virtualbox" do |vb|
      vb.name = "node1_vagrant-vm"
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
      vb.name = "node2_vagrant-vm"
      vb.memory = 512
      vb.cpus = 1
    end

  end

  # VM Ansible Server - Control Node
  config.vm.define "ansible" do |ansible|
    ansible.vm.box = "generic/rocky9"
    ansible.vm.hostname = "ansible"
    ansible.vm.network "private_network", ip: "192.168.50.1"
    ansible.vm.synced_folder ".", "/vagrant"

    ansible.vm.provider "virtualbox" do |vb|
      vb.name = "AnsibleControlNode_vagrant-vm"
      vb.gui = false
      vb.memory = 1024
      vb.cpus = 1
    end

    # Share current folder (local repository) in the VM.
    ansible.vm.synced_folder ".", "/home/vagrant/ansible_project"

    # Install Ansible as root
    ansible.vm.provision "install-ansible", type: "shell", privileged: true, inline: <<-SHELL
      echo "👉 Installing Ansible..."
      sudo dnf install -y epel-release
      sudo dnf install -y ansible python3-pip      
      mkdir -p /home/vagrant/.ssh
      cp /vagrant/.vagrant/machines/node1/virtualbox/private_key /home/vagrant/.ssh/node1_key
      cp /vagrant/.vagrant/machines/node2/virtualbox/private_key /home/vagrant/.ssh/node2_key
      chmod 600 /home/vagrant/.ssh/node1_key
      chmod 600 /home/vagrant/.ssh/node2_key
      chown vagrant:vagrant /home/vagrant/.ssh/node1_key /home/vagrant/.ssh/node2_key
    SHELL

    # Install ansible-lint as vagrant user
    ansible.vm.provision "install-linter", type: "shell", privileged: false, inline: <<-SHELL
      echo "👉 Installing ansible-linter using pip3 --user ..."
      pip3 install --user ansible-lint

      echo 'export PATH=$PATH:$HOME/.local/bin' >> ~/.bashrc
      echo 'export PATH=$PATH:$HOME/.local/bin' >> ~/.bash_profile
      export PATH="$PATH:$HOME/.local/bin"
      echo "👉 Checking ansible-lint installation..."
      ansible-lint --version || echo "⚠️ ansible-lint not found."
    SHELL

    # Running ansible playbook
    ansible.vm.provision "run-ansible-playbook", type: "shell", privileged: false, inline: <<-SHELL
      cd /home/vagrant/ansible_project
      echo "👉 Running Ansible Playbook..."
      ansible-playbook -i /vagrant/inventories/vagrant/hosts.ini /vagrant/playbook.yml
    SHELL
    
  end

end
