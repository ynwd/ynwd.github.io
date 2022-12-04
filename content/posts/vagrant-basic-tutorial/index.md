---
title: "Vagrant basic tutorial"
date: "2022-12-02"
description: "Very step by step article to start a vagrant project."
images: ["posts/vagrant-basic-tutorial/vagrant.png"]
tags: [vagrant]
---

![vagrant](vagrant.png)

Saat development dengan beberapa orang pada project yang sama, biasanya sering terjadi satu aplikasi bisa berjalan baik di localhost. Tapi begitu project itu dipindah ke komputer lain, aplikasi tidak bekerja seperti yang diharapkan.

Vagrant dibuat untuk mengatasi problem itu. Ia memanfaatkan virtualbox untuk membuat sebuah atau beberapa virtual server sekaligus. Kita dapat membuat virtual server yang sama persis dengan yang pertama di komputer lain berdasarkan konfigurasi yang telah dibuat.

1. Install vagrant  

    Download dan install [virtualbox](https://www.virtualbox.org/wiki/Downloads). 
    
    Kemudian download dan install [vagrant](https://developer.hashicorp.com/vagrant/downloads). 
        
    Pilih sesuai OS yang anda gunakan. Ikuti prosesnya langkah demi langkah. Sampai selesai. Ketik perintah di bawah untuk memastikan apakah dia sudah berhasil terinstall.
    
    ```
    vagrant -h
    ```

2. Buat direktori kerja
    ```
    mkdir vagrant && cd vagrant
    ```

    Buat `Vagrantfile`
    ```
    vagrant init hashicorp/bionic64
    ```

    Keterangan:

    - `init`  
        digunakan untuk membuat file `Vagrantfile` yang berisi konfigurasi bawaan (default) virtual machine.

    - `hashicorp/bionic64`  
        nama virtual machine yang tersedia di https://app.vagrantup.com/hashicorp/boxes/bionic64. Kamu juga bisa menggantinya dengan nilai lain. Misalnya: `ubuntu/jammy64`
 
3. Start the Vagrant VM 
    ```
    vagrant up
    ```
    Perintah ini digunakan untuk memulai dan menyediakan virtual machine sesuai yang tertera di `Vagrantfile`.

4. SSH the Vagrant VM
    ```
    vagrant ssh
    ```
    Perintah ini digunakan untuk masuk ke dalam virtual machine. Ketik control-d untuk keluar dari SSH.

5. Enable provisioning with a shell script  
    - Buka `Vagrantfile`. 
    - Cari bagian `config.vm.provision`
    - Hilangkan komen dan tambahkan perintah berikut:
        ```ruby
        # Enable provisioning with a shell script. Additional provisioners such as
        # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
        # documentation for more information about their specific syntax and use.
        config.vm.provision "shell", inline: <<-SHELL
            apt-get update
            apt-get install -y apache2
            service apache2 start
            service apache2 status
        SHELL
        ```
        Konfigurasi ini dipakai untuk menginstall `apache` paket dan menjalankan perintah yang tersedia di sistem operasi ubuntu.

6. Test apache webserver
    - Jalankan: `vagrant up`
    - Masuk ke SSH: `vagrant ssh`
    - Test apache webserver: `curl localhost`
    - Keluar dari ssh dengan control-d.
    - Hapus VM: `vagrant destroy`

7. Networking  
    Buka `Vagrantfile` lagi. Cari dan *un-comment* komentar bagian-bagian ini:
    ```ruby
    # Create a forwarded port mapping which allows access to a specific port
    # within the machine from a port on the host machine. In the example below,
    # accessing "localhost:8080" will access port 80 on the guest machine.
    # NOTE: This will enable public access to the opened port
    config.vm.network "forwarded_port", guest: 80, host: 8080
    # Create a private network, which allows host-only access to the machine
    # using a specific IP.
    config.vm.network "private_network", ip: "192.168.33.10"
    # Create a public network, which generally matched to bridged network.
    # Bridged networks make the machine appear as another physical device on
    # your network.
    config.vm.network "public_network"
    ```

    Dengan konfigurasi di atas, kamu dapat mengakses apache webserver dengan membuka http://192.168.33.10 dari browser.

8. Mengganti Memori dan CPU  
    Cari `config.vm.provider`. Update memori dan CPU:
    ```ruby
    # Provider-specific configuration so you can fine-tune various
    # backing providers for Vagrant. These expose provider-specific options.
    # Example for VirtualBox:
    config.vm.provider "virtualbox" do |vb|
        vb.memory = 1024
        vb.cpus = 1
    end
    ```

9. Mengganti shared folder  
    Cari `config.vm.synced_folder`. Update shared folder.
    ```ruby
    # Share an additional folder to the guest VM. The first argument is
    # the path on the host to the actual folder. The second argument is
    # the path on the guest to mount the folder. And the optional third
    # argument is a set of non-required options.
    config.vm.synced_folder ".", "/vagrant_data"
    ```
    Konfigurasi di atas akan menyebabkan folder di mana vagrant dijalankan, akan bisa diakses via SSH di dalam folder `"/vagrant_data"`.

10. Recreate VM after update
    
    Jangan lupa untuk `destroy` setelah melakukan perubahan di `Vagrantfile`.  
    ```
    vagrant destroy
    ```
    
    Setelah itu jalankan `up` untuk provisioning.  
    ```
    vagrant up
    ```
    
    Bisa juga dengan menjalankan `reload`.  
    ```
    vagrant reload
    ```
    
    Kamu bisa mendapatkan source code artikel ini di sini: https://github.com/ynwd/vagrant
