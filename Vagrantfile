Vagrant.configure("2") do |config|
  # Define the internet-router
  config.vm.define "internet-router" do |internet_router|
    internet_router.vm.box = "debian/bullseye64"
    internet_router.vm.hostname = "internet-router"
    internet_router.vm.network "private_network", ip: "10.1.2.1", virtualbox__intnet: "intnet"
    internet_router.vm.provider "virtualbox" do |v|
      v.name = "internet-router"
      v.memory = 512
      v.cpus = 1
      #v.customize ["modifyvm", :id, "--nic3", "natnetwork"]
      #v.customize ["modifyvm", :id, "--nat-network3", "NatNetwork"]
    end
    internet_router.vm.provision "shell",
      run: "once",
      inline: <<-SHELL
        echo 1 > /proc/sys/net/ipv4/ip_forward
        apt-get update
        apt-get install iptables -y
      SHELL
    internet_router.vm.provision "shell",
      run: "always",
      inline: <<-SHELL
        echo "nameserver 8.8.8.8" > /etc/resolv.conf
        # NAT from private network to Internet
        if ! iptables -C FORWARD -i eth1 -o eth0 -m conntrack --ctstate NEW,ESTABLISHED,RELATED -j ACCEPT 1>/dev/null 2>&1;
          then iptables -A FORWARD -i eth1 -o eth0 -m conntrack --ctstate NEW,ESTABLISHED,RELATED -j ACCEPT
        fi
        if ! iptables -C FORWARD -o eth1 -i eth0 -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT 1>/dev/null 2>&1;
          then iptables -A FORWARD -o eth1 -i eth0 -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
        fi
        if ! iptables -t nat -A POSTROUTING -o eth0 -s 10.1.2.0/24 -j MASQUERADE 1>/dev/null 2>&1;
          then iptables -t nat -A POSTROUTING -o eth0 -s 10.1.2.0/24 -j MASQUERADE
        fi
      SHELL
  end

  # Define router1
  config.vm.define "router1" do |router1|
    router1.vm.box = "debian/bullseye64"
    router1.vm.hostname = "router1"
    router1.vm.network "private_network", ip: "10.1.2.2", virtualbox__intnet: "intnet"
    router1.vm.network "private_network", ip: "192.168.1.1", virtualbox__intnet: "sub1"
    router1.vm.provider "virtualbox" do |v|
      v.name = "router1"
      v.memory = 512
      v.cpus = 1
    end
    router1.vm.provision "shell",
      run: "once",
      inline: <<-SHELL
        echo 1 > /proc/sys/net/ipv4/ip_forward
        apt-get update
        apt-get install iptables -y
      SHELL
    router1.vm.provision "shell",
      run: "always",
      inline: <<-SHELL
        echo "nameserver 8.8.8.8" > /etc/resolv.conf
        ip route del default # Delete nat interface routing
        ip route add default via 10.1.2.1 dev eth1
        # NAT from private network to Internet
        if ! iptables -C FORWARD -i eth2 -o eth1 -m conntrack --ctstate NEW,ESTABLISHED,RELATED -j ACCEPT 1>/dev/null 2>&1;
          then iptables -A FORWARD -i eth2 -o eth1 -m conntrack --ctstate NEW,ESTABLISHED,RELATED -j ACCEPT
        fi
        if ! iptables -C FORWARD -o eth2 -i eth1 -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT 1>/dev/null 2>&1;
          then iptables -A FORWARD -o eth2 -i eth1 -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
        fi
        if ! iptables -t nat -A POSTROUTING -o eth1 -s 192.168.1.0/24 -j MASQUERADE 1>/dev/null 2>&1;
          then iptables -t nat -A POSTROUTING -o eth1 -s 192.168.1.0/24 -j MASQUERADE
        fi
      SHELL
  end

  # Define router2
  config.vm.define "router2" do |router2|
    router2.vm.box = "debian/bullseye64"
    router2.vm.hostname = "router2"
    router2.vm.network "private_network", ip: "10.1.2.3", virtualbox__intnet: "intnet"
    router2.vm.network "private_network", ip: "192.168.1.1", virtualbox__intnet: "sub2"
    router2.vm.provider "virtualbox" do |v|
      v.name = "router2"
      v.memory = 512
      v.cpus = 1
    end
    router2.vm.provision "shell",
      run: "once",
      inline: <<-SHELL
        echo 1 > /proc/sys/net/ipv4/ip_forward
        apt-get update
        apt-get install iptables -y
      SHELL
    router2.vm.provision "shell",
      run: "always",
      inline: <<-SHELL
        echo "nameserver 8.8.8.8" > /etc/resolv.conf
        ip route del default # Delete nat interface routing
        ip route add default via 10.1.2.1 dev eth1
        # NAT from private network to Internet
        if ! iptables -C FORWARD -i eth2 -o eth1 -m conntrack --ctstate NEW,ESTABLISHED,RELATED -j ACCEPT 1>/dev/null 2>&1;
          then iptables -A FORWARD -i eth2 -o eth1 -m conntrack --ctstate NEW,ESTABLISHED,RELATED -j ACCEPT
        fi
        if ! iptables -C FORWARD -o eth2 -i eth1 -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT 1>/dev/null 2>&1;
          then iptables -A FORWARD -o eth2 -i eth1 -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
        fi
        if ! iptables -t nat -A POSTROUTING -o eth1 -s 192.168.1.0/24 -j MASQUERADE 1>/dev/null 2>&1;
          then iptables -t nat -A POSTROUTING -o eth1 -s 192.168.1.0/24 -j MASQUERADE
        fi
      SHELL
  end

  # Define private-server
  config.vm.define "private-server" do |private_server|
    private_server.vm.box = "debian/bullseye64"
    private_server.vm.hostname = "private-server"
    private_server.vm.network "private_network", ip: "10.1.2.4", virtualbox__intnet: "intnet"
    private_server.vm.provider "virtualbox" do |v|
      v.name = "private-server"
      v.memory = 512
      v.cpus = 1
    end
    private_server.vm.provision "shell",
      run: "once",
      inline: <<-SHELL
        apt-get update
        apt-get install iptables unzip -y
      SHELL
    private_server.vm.provision "shell",
      run: "always",
      inline: <<-SHELL
        echo "nameserver 8.8.8.8" > /etc/resolv.conf
        ip route del default # Delete nat interface routing
        ip route add default via 10.1.2.1 dev eth1
      SHELL
  end

  # Define workstation
  config.vm.define "workstation" do |workstation|
    workstation.vm.box = "debian/bullseye64"
    workstation.vm.hostname = "workstation"
    workstation.vm.network "private_network", ip: "192.168.1.10", virtualbox__intnet: "sub1"
    workstation.vm.provider "virtualbox" do |v|
      v.name = "workstation"
      v.memory = 512
      v.cpus = 1
    end
    workstation.vm.provision "shell",
      run: "once",
      inline: <<-SHELL
        apt-get update
        apt-get install iptables unzip -y
      SHELL
    workstation.vm.provision "shell",
      run: "always",
      inline: <<-SHELL
        echo "nameserver 8.8.8.8" > /etc/resolv.conf
        ip route del default # Delete nat interface routing
        ip route add default via 192.168.1.1 dev eth1
      SHELL
  end

  # Define web-server
  config.vm.define "web-server" do |web_server|
    
    web_server.vm.box = "debian/bullseye64"
    web_server.vm.hostname = "web-server"
    web_server.vm.network "private_network", ip: "192.168.1.10", virtualbox__intnet: "sub2"
    web_server.vm.provider "virtualbox" do |v|
      v.name = "web-server"
      v.memory = 512
      v.cpus = 1
    end
    web_server.vm.provision "shell",
      run: "once",
      inline: <<-SHELL
        apt-get update
        apt-get install apache2 unzip -y
        systemctl enable apache2
      SHELL
    web_server.vm.provision "shell",
      run: "always",
      inline: <<-SHELL   
        echo "nameserver 8.8.8.8" > /etc/resolv.conf
        ip route del default # Delete nat interface routing
        ip route add default via 192.168.1.1 dev eth1
      SHELL
  end
end
