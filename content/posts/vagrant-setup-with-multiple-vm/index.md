---
title: "Vagrant setup with multiple virtual machine"
date: 2022-12-05T16:42:35+07:00
description: "Simple steps to setup vagrant with multiple virtual machine"
images: ["posts/vagrant-basic-tutorial/vagrant.png"]
tags: [vagrant]
---

```ruby
Vagrant.configure("2") do |config|
  config.vm.define "node" do |node|
    node.vm.box = "generic/alpine317"
    node.vm.network "private_network", ip: "192.168.33.10"
    node.vm.synced_folder ".", "/home/vagrant/webapp"

    node.vm.provider :virtualbox do |v|
      v.name    = "node"
      v.memory  = 512
      v.cpus    = 1
    end

    node.vm.provision "shell", inline: <<-SHELL
      apk update
      apk add nodejs npm
    SHELL
  end

  config.vm.define "postgres" do |postgres|
    postgres.vm.box = "generic/alpine317"
    postgres.vm.network "private_network", ip: "192.168.33.11"
    
    postgres.vm.provider :virtualbox do |v|
      v.name    = "postgres"
      v.memory  = 512
      v.cpus    = 1
    end

    postgres.vm.provision "shell", inline: <<-SHELL
      apk update
      apk add postgresql postgresql-client
      /etc/init.d/postgresql setup
      /etc/init.d/postgresql start && rc-update add postgresql default
      createuser -U postgres -s root
      createdb root
      createuser -U postgres -s vagrant
      createdb vagrant
    SHELL
  end

  config.vm.define "redis" do |redis|
    redis.vm.box = "generic/alpine317"
    redis.vm.network "private_network", ip: "192.168.33.12"
    
    redis.vm.provider :virtualbox do |v|
      v.name    = "redis"
      v.memory  = 512
      v.cpus    = 1
    end

    redis.vm.provision "shell", inline: <<-SHELL
      apk update
      apk add redis
      sed -i 's/bind 127.0.0.1 -::1/# bind 127.0.0.1 -::1/g' /etc/redis.conf
      sed -i 's/# requirepass foobared/requirepass root/g' /etc/redis.conf
      rc-service redis start 
      rc-update add redis default
    SHELL
  end

end

```
