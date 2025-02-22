Vagrant.configure("2") do |config|
    config.vm.box = "ubuntu/bionic64"
    config.vm.network "forwarded_port", guest: 8080, host: 8080
    config.vm.synced_folder ".", "/vagrant_data"
    config.vm.provider "virtualbox" do |vb|
      vb.memory = "4096"
    end
  end
  