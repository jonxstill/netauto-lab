# -*- mode: ruby -*-
# vi: set ft=ruby :

# Specify minimum Vagrant version and Vagrant API version
Vagrant.require_version ">= 1.6.0"
VAGRANTFILE_API_VER = "2"

# Plugins we require
required_plugins = %w(vagrant-junos vagrant-vbguest vagrant-host-shell)

##### START Helper functions
def install_ssh_key()
  puts "Adding ssh key to the ssh agent"
  puts "ssh-add #{Vagrant.source_root}/keys/vagrant"
  system "ssh-add #{Vagrant.source_root}/keys/vagrant"
end

def install_plugins(required_plugins)
  plugins_to_install = required_plugins.select { |plugin| not Vagrant.has_plugin? plugin }
  if not plugins_to_install.empty?
    puts "Installing plugins: #{plugins_to_install.join(' ')}"
    if system "vagrant plugin install #{plugins_to_install.join(' ')}"
      exec "vagrant #{ARGV.join(' ')}"
    else
      abort "Installation of one or more plugins has failed. Aborting."
    end
  end
end
##### END Helper functions

# Install ssh key
# 
# Uncomment the next line if you're using ssh-agent
# install_ssh_key

# Check certain plugins are installed
install_plugins required_plugins

# Require YAML module
require 'yaml'
 
# Read YAML file with box details
vagrant_root = File.dirname(__FILE__)
hosts = YAML.load_file(vagrant_root + '/topology.yml')

# Lab definition begins here
Vagrant.configure(VAGRANTFILE_API_VER) do |config|
  config.vbguest.auto_update = false

  # Iterate through entries in YAML file
  hosts.each do |host|
    config.vm.define host["name"] do |srv|
      srv.vm.box = host["box"]

      if host.key?("forwarded_ports")
        host["forwarded_ports"].each do |port|
          srv.vm.network :forwarded_port, guest: port["guest"], host: port["host"], id: port["name"]
        end
      end

      if host.key?("links")
        host["links"].each do |link|
          ipaddr = "169.254.1.11"
          srv.vm.network "private_network", virtualbox__intnet: link["name"], ip: (defined?(link["ip"]) ? link["ip"] : ipaddr), auto_config: (/vEOS/.match(host['box']) ? false : true)
          if link.key?("name")
            if link["name"] == "mgmt"
              $mgmtip = link["ip"]
            end
          end
        end
      end

      if /vEOS/.match(host['box'])
        $script = <<-SCRIPT
FastCli -p 15 -c "configure
hostname $1
vrf definition MGMT
rd 1:1
interface Eth1
no switchport
no shutdown
vrf forwarding MGMT
ip address $2 255.255.255.0"
exit
management api http-commands
no shutdown
vrf MGMT
no shutdown
SCRIPT

        srv.vm.provision "shell" do |s|
          s.inline = $script
          s.args = [host["name"], $mgmtip]
        end
      end
      if /ubuntu/.match(host['box'])
        $script = <<-SCRIPT
export DEBIAN_FRONTEND=noninteractive
sudo hostnamectl set-hostname $1
cp /etc/hosts /tmp/hosts
sudo cat /tmp/hosts|sed 's/vagrant/nms/g' > /etc/hosts
sudo apt-get install software-properties-common -y
sudo apt-add-repository ppa:ansible/ansible -y
sudo apt-get update
sudo apt-get install ansible -y
sudo apt-get install python-pip -y
sudo pip install ntc-ansible
mkdir -p /home/vagrant/.ssh
cp /vagrant/ssh-config /home/vagrant/.ssh/config
SCRIPT

        srv.vm.provision "shell" do |s|
          s.inline = $script
          s.args = host["name"]
        end
      end
    end
  end
end

