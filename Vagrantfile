Vagrant.configure("2") do |config|
    config.vm.box = "ubuntu/bionic64"
  
    # Use Bridged Network Adapter
    config.vm.network "public_network", bridge: "en0: Wi-Fi (AirPort)"
  
    # Increase Memory Allocation to 4GB
    config.vm.provider "virtualbox" do |vb|
      vb.memory = "4096"  # Set memory to 4096MiB (4GB)
    end
  
    # Provision script to start Minikube with none driver and install cri-dockerd
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
  
      # Install conntrack
      sudo apt-get install -y conntrack
  
      # Install crictl
      VERSION="v1.26.0"  # You can change this to the required version
      curl -LO https://github.com/kubernetes-sigs/cri-tools/releases/download/$VERSION/crictl-$VERSION-linux-amd64.tar.gz
      sudo tar -C /usr/local/bin -xzf crictl-$VERSION-linux-amd64.tar.gz
      sudo chmod +x /usr/local/bin/crictl
  
      # Download and install a newer version of Go
      wget https://golang.org/dl/go1.17.3.linux-amd64.tar.gz || curl -LO https://golang.org/dl/go1.17.3.linux-amd64.tar.gz
      sudo tar -C /usr/local -xzf go1.17.3.linux-amd64.tar.gz
      echo "export PATH=$PATH:/usr/local/go/bin" >> /home/vagrant/.profile
      echo "export GOPATH=/home/vagrant/go" >> /home/vagrant/.profile
      echo "export PATH=$PATH:$GOPATH/bin" >> /home/vagrant/.profile
      source /home/vagrant/.profile
  
      # Set up Go workspace
      mkdir -p /home/vagrant/go/src /home/vagrant/go/bin
  
      # Download prebuilt binary for cri-dockerd
      wget https://github.com/Mirantis/cri-dockerd/releases/download/v0.3.16/cri-dockerd-0.3.16.amd64.tgz
      tar -xzf cri-dockerd-0.3.16.amd64.tgz
      sudo mv cri-dockerd/cri-dockerd /usr/local/bin/
      sudo chmod +x /usr/local/bin/cri-dockerd
  
      # Create and start cri-dockerd systemd service
      sudo tee /etc/systemd/system/cri-dockerd.service <<EOF
      [Unit]
      Description=CRI Interface for Docker Application Engine
      Documentation=https://github.com/Mirantis/cri-dockerd
      Wants=docker.socket
      After=docker.service
  
      [Service]
      ExecStart=/usr/local/bin/cri-dockerd --container-runtime-endpoint fd:// --network-plugin cni --pod-infra-container-image=k8s.gcr.io/pause:3.5
      Restart=always
      StartLimitBurst=3
      StartLimitInterval=60s
  
      [Install]
      WantedBy=multi-user.target
      EOF
  
      sudo systemctl daemon-reload
      sudo systemctl enable cri-dockerd.service
      sudo systemctl start cri-dockerd.service
  
      # Start Minikube with none driver
      sudo minikube start --driver=none
  
      # Enable Minikube addons (if needed)
      sudo minikube addons enable dashboard
  
      # Set up environment variables (optional)
      echo "export KUBECONFIG=/home/vagrant/.kube/config" >> /home/vagrant/.profile
    SHELL
  end
  