---
title: "Vagrant setup for node.js"
date: 2022-12-04T17:20:21+07:00
description: "Simple steps to setup vagrant for node.js application"
images: ["posts/vagrant-setup-for-nodejs/node.png"]
tags: [vagrant]
---

![vagrant](node.png)

> You can find the source-code of this tutorial at: [https://github.com/ynwd/vagrant-node](https://github.com/ynwd/vagrant-node)


1. Create application folder
    ```
    mkdir vagrant-node && cd vagrant-node
    ```

2. Init node.js application
    ```
    npm init
    ```

3. Install express
    ```
    npm install express
    ```

4. Create main.js
    ```js
    const express = require('express')
    const app = express()
    const port = 3000

    app.get('/', (req, res) => {
        res.send('Hello World!')
    })

    app.listen(port, () => {
        console.log(`Example app listening on port ${port}`)
    })
    ```

5. Init vagrantfile
    ```
    vagrant init ubuntu/jammy64
    ```

6. Update vagrantfile
    ```ruby
    Vagrant.configure("2") do |config|
        config.vm.box = "ubuntu/jammy64"
        config.vm.network "private_network", ip: "192.168.33.10"
        config.vm.provision "shell", inline: <<-SHELL
            curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.2/install.sh | bash
            source ~/.nvm/nvm.sh
            echo "source ~/.nvm/nvm.sh && cd /vagrant" > ~/.bashrc
            nvm install --lts &> /dev/null
            nvm alias default stable
            node --version
        SHELL
    end

    ```

7. Start vagrant-vm
    ```
    vagrant up
    ```

8. SSH vagrant-vm
    ```
    vagrant ssh
    ```

    Output
    ```
    Welcome to Ubuntu 22.04.1 LTS (GNU/Linux 5.15.0-53-generic x86_64)

    * Documentation:  https://help.ubuntu.com
    * Management:     https://landscape.canonical.com
    * Support:        https://ubuntu.com/advantage

    System information as of Sun Dec  4 10:49:46 UTC 2022

    System load:  0.0               Processes:               101
    Usage of /:   4.1% of 38.70GB   Users logged in:         0
    Memory usage: 21%               IPv4 address for enp0s3: 10.0.2.15
    Swap usage:   0%                IPv4 address for enp0s8: 192.168.33.10


    0 updates can be applied immediately.


    The list of available updates is more than a week old.
    To check for new updates run: sudo apt update

    Last login: Sun Dec  4 10:17:23 2022 from 10.0.2.2
    vagrant@ubuntu-jammy:/vagrant$
    ```

9. Login as root
    ```
    sudo su
    ```
    
10. Run application
    ```
    node main.js
    ```
    You can access node.js application from localhost with ip: [http://192.168.33.10:3000](http://192.168.33.10:3000).

    You can also edit `main.js` file directly.
