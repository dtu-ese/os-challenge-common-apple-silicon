Vagrant.configure("2") do |config|

  config.vm.define "server" do |server|
    server.vm.box = "bytesguy/ubuntu-server-20.04-arm64"
    server.vm.box_version = "1.0.0"
    server.vm.provision :shell, path: "bootstrap.sh"
    server.vm.hostname = "server"
    server.vm.network :private_network, ip: "192.168.101.10"
    server.vm.synced_folder "./", "/home/vagrant/os-challenge-common/"
    server.vm.provider :vmware_fusion do |provider|
      provider.gui = true
      provider.vmx["memsize"] = "512"
      provider.vmx["numbcpus"] = "4"
      provider.vmx["ethernet1.pcislotnumber"] = "36"
    end
  end

  config.vm.define "client" do |client|
    client.vm.box = "bytesguy/ubuntu-server-20.04-arm64"
    client.vm.box_version = "1.0.0"
    client.vm.provision :shell, path: "bootstrap.sh"
    client.vm.hostname = "client"
    client.vm.network :private_network, ip: "192.168.101.11"
    client.vm.synced_folder "./", "/home/vagrant/os-challenge-common/"
    client.vm.provider :vmware_fusion do |provider|
      provider.gui = true
      provider.vmx["memsize"] = "512"
      provider.vmx["numbcpus"] = "1"
      provider.vmx["ethernet1.pcislotnumber"] = "36"
    end
  end

end