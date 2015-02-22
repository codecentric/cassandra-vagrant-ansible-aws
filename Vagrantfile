# -*- mode: ruby -*-
# vi: set ft=ruby :

#Require aws credentials
require 'yaml'
aws_config = YAML::load_file("#{File.dirname(File.expand_path(__FILE__))}/aws-config.yml")

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  
  # Use ansible for provisioning
  config.vm.provision :ansible, :playbook => 'ansible/ansible-playbook.yml'
  
  aws_config["datacenters"].each do |datacenter_info|
    
    datacenter_info["number_of_nodes"].times do |node_number|
    
      node_name = aws_config["node_name_prefix"] + "-" + datacenter_info["region"] + "-" + node_number.to_s
    
      config.vm.define(node_name) do |node|  
      
          node.vm.box = "ubuntu/trusty64"
          node.vm.hostname = node_name

          node.vm.provider :virtualbox do |v|
            v.name = node_name
          end

          node.vm.provider :aws do |aws, override|
            aws.access_key_id = aws_config["key"]
            aws.secret_access_key = aws_config["secret"]
            
            aws.keypair_name = datacenter_info["keyname"]
        
            aws.ami = datacenter_info["ami"]
            aws.region = datacenter_info["region"]
            aws.instance_type = datacenter_info["instance_type"]
            aws.security_groups = ["default", datacenter_info["security_group"]]

            override.vm.box = "dummy"
            override.ssh.username = "ubuntu"
            override.ssh.private_key_path = datacenter_info["keypath"]
          end
        end
    
    end
  end

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

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
end
