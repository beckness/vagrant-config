# -*- mode: ruby -*-
# vi: set ft=ruby :
 
# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

# Customized for multiple VMs jmb@bnr

Vagrant.configure(2) do |config|

# Global hostmanager settings - enable /etc/sudoers.d/vagrant_hostmanager if needed
# TODO: move parts into config file to allow public IPs
config.hostmanager.enabled = true
config.hostmanager.manage_host = true
config.hostmanager.manage_guest = true
config.hostmanager.ignore_private_ip = false
config.hostmanager.include_offline = true

# Load up our configs
require 'yaml'
vconfig = YAML.load_file("vhosts.yml")

unless Vagrant.has_plugin?("vagrant-hostmanager")
  raise 'hostmanager plugin is not installed!'
end

cached_addresses = {}
config.hostmanager.ip_resolver = proc do |vm, resolving_vm|
  if cached_addresses[vm.name].nil?
    if hostname = (vm.ssh_info && vm.ssh_info[:host])
      vm.communicate.execute("hostname -I | cut -d ' ' -f 2") do |type, contents|
        cached_addresses[vm.name] = contents.split("\n").first[/(\d+\.\d+\.\d+\.\d+)/, 1]
      end
    end
  end
  cached_addresses[vm.name]
end


# Loop through each VM.  Configs live in hosts.yaml.
# Vagrant-written configs live in ~/.vagrant/machines

vconfig.each_with_index do |(hostname, cfg), index|

  config.vm.define hostname do |config|
 
      config.vm.box = #{cfg[:type]}
      config.vm.hostname = hostname
      config.vm.network "private_network", ip: "#{cfg[:ip]}"
      config.vm.provider "virtualbox" do |vb|
       vb.cpus = "#{cfg[:cpus]}"
       vb.memory = "#{cfg[:mem]}"
       vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
       vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
       vb.gui = false # change to true if you want console in virtualbox for debug
      end
   end
  end

end
