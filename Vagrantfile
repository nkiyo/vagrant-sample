# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "generic/rocky8"
  # config.disksize.size = '50GB'

  # 初回、ホスト側マシン(Windows) 再起動が必要だった
  config.vm.network "private_network", ip: "192.168.33.10"

  config.vm.provider "virtualbox" do |vb|
    # vb.gui = true
    vb.memory = "4096"
  end

  # test file copy
  config.vm.provision "file", source: "./hoge.txt", destination: "~/hage.txt"

  # Install podman and docker_compose
  #   => https://rheb.hatenablog.com/entry/podman3_docker_compose
  # shellコマンドは、rootユーザで実行される。sudo不要とのこと
  #   => https://qiita.com/pasela/items/906291647c4f97b9a7c7
  # rootユーザだと、/usr/local/bin/docker-compose で実行する必要あり。PATHが通ってないため
  config.vm.provision "shell", inline: <<-SHELL
    # basic command
    yum update
    yum install -y tmux git cifs-utils
    # git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
    # ~/.fzf/install
    # git clone --depth 1 https://github.com/rupa/z.git ~/.z

    # GUI
    # sudo yum group install -y "Server with GUI"

    # podman
    yum install -y podman podman-plugins
    python3 -m pip install --upgrade pip
    python3 -m pip install docker-compose
    systemctl enable podman.socket
    # sudo systemctl start podman.socket
    echo 'root:root' | sudo chpasswd
    echo 'export DOCKER_HOST=unix:/run/podman/podman.sock' >> /root/.bashrc

    # nodejs
    # curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | sudo bash
    # source ~/.bashrc
    # sudo nvm install stable --latest-npm
    # sudo nvm alias default stable

    # python
    # sudo yum install -y python3.11
    
  SHELL
end
