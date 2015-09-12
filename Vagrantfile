# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrant Debian Proxy VM
# Sets up a simple VM with a Squid proxy/cache to speed up local package installs for Debian/Ubuntu
# Brian Cantoni
# ref: http://www.garron.me/en/blog/ubuntu-deb-proxy-cache.html

# Adjustable settings
CFG_MEMSIZE = "1000"      # max memory for each VM
CFG_TZ = "US/Pacific"     # timezone, like US/Pacific, US/Eastern, UTC, Europe/Warsaw, etc.
CFG_IP = "10.211.54.100"  # private IP address

node_script = <<SCRIPT
#!/bin/bash

# set timezone
echo "#{CFG_TZ}" > /etc/timezone
dpkg-reconfigure -f noninteractive tzdata

# install a few base packages
apt-get update
apt-get install vim curl python-pip squid-deb-proxy squid-deb-proxy-client -y

sed -i 's/http_access deny !to_ubuntu_mirrors/#http_access deny !to_ubuntu_mirrors/' /etc/squid-deb-proxy/squid-deb-proxy.conf
sed -i 's/#http_access allow !to_ubuntu_mirrors/http_access allow !to_ubuntu_mirrors/' /etc/squid-deb-proxy/squid-deb-proxy.conf

sed -i 's/cache deny !to_ubuntu_mirrors/#cache deny !to_ubuntu_mirrors/' /etc/squid-deb-proxy/squid-deb-proxy.conf
sed -i 's/#cache allow !to_ubuntu_mirrors/cache allow !to_ubuntu_mirrors/' /etc/squid-deb-proxy/squid-deb-proxy.conf

SCRIPT

# VM configurations
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define :debcache do |x|
    x.vm.box = "ubuntu/trusty64"

    x.vm.provider "vmware_fusion" do |v|
      v.vmx["memsize"] = CFG_MEMSIZE
    end

    x.vm.provider :virtualbox do |v|
      v.name = "debcache"
      v.customize ["modifyvm", :id, "--memory", CFG_MEMSIZE]
    end

    x.vm.network :private_network, ip: CFG_IP
    x.vm.hostname = "debcache"
    x.vm.provision :shell, :inline => node_script
  end
end
