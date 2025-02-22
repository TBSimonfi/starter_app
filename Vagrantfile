Vagrant.configure("2") do |config|
    config.vm.box = "ubuntu/bionic64"
  
    # Use Bridged Network Adapter
    config.vm.network "public_network", bridge: "en0: Wi-Fi (AirPort)"
  
    # Provision script to start Minikube
    config.vm.provision "shell", inline: <<-SHELL
      # Update package lists
      sudo apt-get update
  
      # Install Docker if not already installed
      if ! command -v docker &> /dev/null
      then
          sudo apt-get install -y docker.io
      fi
  
      # Install Minikube if not already installed
      if ! command -v minikube &> /dev/null
      then
          curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
          sudo install minikube-linux-amd64 /usr/local/bin/minikube
      fi
  
      # Start Minikube
      sudo minikube start --driver=docker
  
      # Enable Minikube addons (if needed)
      sudo minikube addons enable dashboard
  
      # Set up environment variables (optional)
      echo "export KUBECONFIG=/home/vagrant/.kube/config" >> /home/vagrant/.profile
    SHELL
  end
  