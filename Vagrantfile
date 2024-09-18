Vagrant.configure("2") do |config|
  # Domyślna maszyna bazowa
  config.vm.box = "debian/bookworm64"
 
  # Maszyna 1
  config.vm.define "srv1" do |srv1|
    srv1.vm.network "forwarded_port", guest: 80, host: 8080
    srv1.vm.network "public_network", bridge: "Ethernet 2", ip: "192.168.1.110", mac: "080027D14A11"
    srv1.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.name = "srv1"
      vb.customize ["modifyvm", :id, "--uartmode1", "disconnected"]
    end
    srv1.vm.provision "ansible" do |ansible|
      ansible.playbook = "ansible/hostname.yml"  
      ansible.extra_vars = { hostname: "srv1" }
    end
  end

  # Maszyna 2
  config.vm.define "srv2" do |srv2|
    srv2.vm.network "forwarded_port", guest: 80, host: 8081
    srv2.vm.network "public_network", bridge: "Ethernet 2", ip: "192.168.1.111", mac: "080027D14A12"
    srv2.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.name = "srv2"
      vb.customize ["modifyvm", :id, "--uartmode1", "disconnected"]
    end
    srv2.vm.provision "ansible" do |ansible|
      ansible.playbook = "ansible/hostname.yml"
      ansible.extra_vars = { hostname: "srv2" }
    end
  end

  # Maszyna 3
  config.vm.define "srv3" do |srv3|
    srv3.vm.network "forwarded_port", guest: 80, host: 8082
    srv3.vm.network "public_network", bridge: "Ethernet 2", ip: "192.168.1.112", mac: "080027D14A13"
    srv3.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.name = "srv3"
      vb.customize ["modifyvm", :id, "--uartmode1", "disconnected"]
    end
    srv3.vm.provision "ansible" do |ansible|
      ansible.playbook = "ansible/hostname.yml"
      ansible.extra_vars = { hostname: "srv3" }
    end
  end
end
