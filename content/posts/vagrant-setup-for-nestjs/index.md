---
title: "Vagrant setup for nest.js"
date: 2022-12-05T12:48:38+07:00
description: "Simple steps to setup vagrant for nest.js application"
images: ["posts/vagrant-setup-for-nestjs/nest.jpg"]
tags: [vagrant]
---

![vagrant](nest.jpg)

> You can find the source-code of this tutorial at: [https://github.com/ynwd/vagrant-nest](https://github.com/ynwd/vagrant-nest)


1. Update `Vagrantfile`
    ```ruby
    Vagrant.configure("2") do |config|
        config.vm.box = "ubuntu/jammy64"
        config.vm.network "private_network", ip: "192.168.33.10"
        config.vm.provision "shell", privileged: false, inline: <<-SHELL
            curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.2/install.sh | bash
            source ~/.nvm/nvm.sh
            echo "source ~/.nvm/nvm.sh && cd /vagrant" > ~/.bashrc
            nvm install --lts &> /dev/null
            nvm alias default stable
            node --version
        SHELL
    end
    ```

    Please note that: `privileged: false` is used to make `vagrant` as root user.

2. Run vagrant-vm
    ```
    vagrant up
    ```

3. SSH vagrant-vm
    ```
    vagrant ssh
    ```

4. Run nest application
    ```
    npm start
    ```