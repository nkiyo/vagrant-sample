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

  # copy local files
  config.vm.provision "file", source: "./dotfiles", destination: "/home/vagrant/dotfiles"
  config.vm.provision "file", source: "./src", destination: "/home/vagrant/src"

  # Install podman and docker_compose
  #   => https://rheb.hatenablog.com/entry/podman3_docker_compose
  # shellコマンドは、rootユーザで実行される。sudo不要とのこと
  #   => https://qiita.com/pasela/items/906291647c4f97b9a7c7
  # rootユーザのとき、/usr/local/bin/docker-compose で実行する必要あり。PATHが通ってないため
  #
  # cd ~/src
  # /usr/local/bin/docker-compose up
  # したあと、
  # ホストOSからブラウザで http://192.168.33.10:8080 にアクセスして、Wordpressの画面が表示できればOK
  config.vm.provision "shell", inline: <<-SHELL
    # create symlink to /home/vagrant
    ln -sf /home/vagrant/dotfiles/tmux.conf /root/.tmux.conf
    ln -sf /home/vagrant/dotfiles/tigrc /root/.tigrc
    ln -sf /home/vagrant/src /root/

    # ssh as root
    echo 'root:root' | chpasswd
    sed -i 's/^PasswordAuthentication no$/PasswordAuthentication yes/g' /etc/ssh/sshd_config
    systemctl restart sshd

    # basic command
    yum update
    yum install -y tmux git tig cifs-utils

    # podman + docker-compose
    yum install -y podman podman-plugins
    python3 -m pip install --upgrade pip
    python3 -m pip install docker-compose
    systemctl enable podman.socket
    systemctl start podman.socket
    # `unix:/run/podman/podman.sock' >> /root/.bashrc`だと、lazydockerがエラーを出した
    echo 'export DOCKER_HOST=unix:///run/podman/podman.sock' >> /root/.bashrc

    ## nodejs
    ## curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | sudo bash
    ## source ~/.bashrc
    ## sudo nvm install stable --latest-npm
    ## sudo nvm alias default stable

    ## python
    ## sudo yum install -y python3.11
    
  SHELL

  # config.ssh.username = 'root'
  # config.ssh.password = 'root'
  # config.ssh.insert_key = false
end
