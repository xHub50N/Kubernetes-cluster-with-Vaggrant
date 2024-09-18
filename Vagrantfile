Vagrant.configure("2") do |config|
  # Domyślna maszyna bazowa
  config.vm.box = "debian/bookworm64"  # Sytem operacyjny

  # Maszyna 1
  config.vm.define "srv1" do |srv1| # Nazwa maszyny
    srv1.vm.network "forwarded_port", guest: 80, host: 8080
    srv1.vm.network "public_network", bridge: "Ethernet 2", ip: "192.168.1.110", mac: "080027D14A11" # Host będzie korzystać z sieci bridge i został mu nadany adres IP i MAC
    srv1.vm.provider "virtualbox" do |vb| # Wykorzystanie VirtualBoxa
      vb.memory = "2048" # Zadeklarowania pamięć RAM
      vb.name = "srv1"
      vb.customize ["modifyvm", :id, "--uartmode1", "disconnected"]
    end
    srv1.vm.provision "ansible" do |ansible|
      ansible.playbook = "ansible/hostname.yml"  # Dołączenie zadań Ansible
      ansible.extra_vars = { hostname: "srv1" } # Nazwa maszyny na potrzeby Ansible
    end
  end

  # Maszyna 2
  config.vm.define "srv2" do |srv2| # Nazwa maszyny
    srv2.vm.network "forwarded_port", guest: 80, host: 8081
    srv2.vm.network "public_network", bridge: "Ethernet 2", ip: "192.168.1.111", mac: "080027D14A12" # Host będzie korzystać z sieci bridge i został mu nadany adres IP i MAC
    srv2.vm.provider "virtualbox" do |vb| # Wykorzystanie VirtualBoxa
      vb.memory = "2048" # Zadeklarowania pamięć RAM
      vb.name = "srv2"
      vb.customize ["modifyvm", :id, "--uartmode1", "disconnected"]
    end
    srv2.vm.provision "ansible" do |ansible|
      ansible.playbook = "ansible/hostname.yml" # Dołączenie zadań Ansible
      ansible.extra_vars = { hostname: "srv2" } # Nazwa maszyny na potrzeby Ansible
    end
  end

  # Maszyna 3
  config.vm.define "srv3" do |srv3| # Nazwa maszyny
    srv3.vm.network "forwarded_port", guest: 80, host: 8082
    srv3.vm.network "public_network", bridge: "Ethernet 2", ip: "192.168.1.112", mac: "080027D14A13" # Host będzie korzystać z sieci bridge i został mu nadany adres IP i MAC
    srv3.vm.provider "virtualbox" do |vb| # Wykorzystanie VirtualBoxa
      vb.memory = "2048" # Zadeklarowania pamięć RAM
      vb.name = "srv3"
      vb.customize ["modifyvm", :id, "--uartmode1", "disconnected"]
    end
    srv3.vm.provision "ansible" do |ansible|
      ansible.playbook = "ansible/hostname.yml" # Dołączenie zadań Ansible
      ansible.extra_vars = { hostname: "srv3" } # Nazwa maszyny na potrzeby Ansible
    end
  end
end
