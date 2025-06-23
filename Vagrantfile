Vagrant.configure("2") do |config|
  config.vm.box = "generic/rocky9"
  
  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "forwarded_port", guest: 443, host: 8443

  config.vm.provider "virtualbox" do |vb|
    vb.name = "day6-challenge-rockyLinux9_vagrant-vm"
    vb.gui = true
    vb.memory = "1024"
    vb.cpus = 2
  end  
end

