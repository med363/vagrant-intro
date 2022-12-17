``bash
vagrant init
```

```bash
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.
  ccommon = <<-SHELL
  apt-get update
  apt-get install -y ansible
  SHELL
  
  #elt etchost initiallement vide =>variabe ruby
  etcHosts = ""

  config.vm.box = "ubuntu/trusty64"

  NODES = [
  { :hostname => "master", :ip => "192.168.56.2", :cpus => 2, :mem => 2048, :type => "master" },
  { :hostname => "web1", :ip => "192.168.56.3", :cpus => 2, :mem => 1024, :type => "wb1" },
  { :hostname => "web2", :ip => "192.168.56.4", :cpus => 2, :mem => 1024, :type => "wb2" }
   ]

  NODES.each do |node|
   etcHosts += "echo '" + node[:ip] + "   " + node[:hostname] + "' >> /etc/hosts" + "\n"
  end #end NODES
     
#Multi-machine config
  NODES.each do |node|
   config.vm.define node[:hostname] do |cfg|
   cfg.vm.hostname = node[:hostname]
   cfg.vm.network "private_network", ip: node[:ip]
   cfg.vm.provider "virtualbox" do |v|
	v.customize [ "modifyvm", :id, "--cpus", node[:cpus] ]
        v.customize [ "modifyvm", :id, "--memory", node[:mem] ]
#        v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
#        v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
        v.customize ["modifyvm", :id, "--name", node[:hostname] ]
   end 
   cfg.vm.provision :shell, :inline => ccommon
   cfg.vm.provision :shell, :inline => etcHosts
   cfg.vm.provision "shell" , inline: <<-SHELL
#partage de cle public
    sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/g' /etc/ssh/sshd_config
    service ssh restart
   SHELL
  end
   config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
    ansible.groups = {
      "master" => [node[:hostname] == "master"],
#      "group2" => ["machine2"],
      "webs" => [node[:hostname] == "wb[1:2]"],
#      "group4" => ["other_node-[a:d]"], # silly group definition
      "all_groups:children" => ["master", "webs"]
      }
   end
 end
   

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
#  config.vm.box = "base"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
```
