# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/trusty64" # Ubuntu 14.04 LTS (64)

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network "forwarded_port", guest: 80, host: 8080 # HTTP
  config.vm.network "forwarded_port", guest: 443, host: 4443 # HTTPS
  config.vm.network "forwarded_port", guest: 22222, host: 22222 # To get access to Adminer/Admin & PMA with ee

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # required for nfs
  config.vm.network "private_network", ip: "192.168.33.10" # OR 172.28.128.3

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"
  # config.vm.network "public_network", type: "dhcp", bridge: 'en0: Wi-Fi (AirPort)'

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder "~/Projects/DEV-Projects/Vagrant/Vagrant_solosik", "/var/www", type: "nfs", owner: "root", group: "root" # to access /var/www of Vagrant box from your machine at dev/ location

  # Defaults for VirtualBox:
  config.vm.provider "virtualbox" do |vb|
    vb.gui = false # Display the VirtualBox GUI when booting the machine
    v.name = "1_512_Trusty64" # VB Name
    v.cpus = "1" # CPUs
    vb.memory = "512" # Customize the amount of memory on the VM
  end
  
  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies              
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   sudo apt-get update
  #   sudo apt-get install -y apache2
  # SHELL

  # Hosts (hostupdater)
  config.vm.hostname = "www.example.dev"
  config.hostsupdater.aliases = ["example.dev", "test.example.dev"]
  config.hostsupdater.remove_on_suspend = true

  # Enable port forwarding on startup
  config.trigger.after [:provision, :up, :reload] do
    system(
      'echo "
        rdr pass on lo0 inet proto tcp from any to 127.0.0.1 port 80 -> 127.0.0.1 port 8080
        rdr pass on lo0 inet proto tcp from any to 127.0.0.1 port 443 -> 127.0.0.1 port 4443
        rdr pass on lo0 inet proto tcp from any to 127.0.0.1 port 22222 -> 127.0.0.1 port 22222
     " | sudo pfctl -ef - > /dev/null 2>&1; echo "==> Fowarding Ports: 80 -> 8080, 443 -> 4443, 22222 -> 22222 & Enabling pf"'
    )
  end

  # Disable port forwarding on shutdown
  config.trigger.after [:halt, :destroy] do
    system("sudo pfctl -df /etc/pf.conf > /dev/null 2>&1; echo '==> Removing Port Forwarding & Disabling pf'")
  end

end
