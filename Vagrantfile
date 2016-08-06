# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.define :stns_server do |stns_server|
    # please specify url of box file that you use.
    # box list -> http://www.vagrantbox.es/
    # or other box.
    stns_server.vm.box = "debian/jessie64"
    stns_server.vm.network :private_network, ip: "192.168.20.5"
  end

  config.vm.define :stns_client do |stns_client|
    stns_client.vm.box = "debian/jessie64"
    stns_client.vm.network :private_network, ip: "192.168.20.6"
  end

  config.vm.define :ansible do |ansible|
    ansible.vm.box = "debian/jessie64"
    ansible.vm.network :private_network, ip: "192.168.20.3"
  end

end
