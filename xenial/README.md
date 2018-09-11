## vagrant image of ubuntu 


# Overview 
This vagrant file here does the following things

* Downloads the `VM_IMAGE = "ubuntu/xenial64"`  for the xenial 64 bit server edition 

* Installs docker using docker provisioner 

``` ruby 

      config.vm.provision "docker" do |d|
        puts("This VM will have docker in it")
        #puts ("This image will have docker installation")
      end
```

* Sets the locales in the images to "en_US.UTF-8" using below code 

``` ruby 

   config.vm.provision "shell", name: "Generating locales and running apt-get update",  inline: <<-SHELL
     echo "LANG=en_US.UTF-8" > /etc/default/locale
     echo "LC_ALL=en_US.UTF-8" >> /etc/default/locale
     echo "LANGUAGE=en_US.UTF-8" >> /etc/default/locale
     locale-gen
     apt-get update -qq || true
   SHELL
```

* Adds the systemd dropin for docker.socket unit to expose docker api port. For any linux system adding the `/etc/systemd/system/docker.socket.d/docker-override.conf` file with `ListenStream` set to desired port will expose the docker api. 

``` ruby 
$docker_expose_script = <<-SCRIPT
mkdir -p /etc/systemd/system/docker.socket.d
echo "[Socket]" > /etc/systemd/system/docker.socket.d/docker-override.conf
echo "ListenStream=2376" >> /etc/systemd/system/docker.socket.d/docker-override.conf
config.vm.provision "shell", inline: $docker_expose_script, name: "adding docker dropin systemd unit modification"
``` 

# Configuration 

This image exposes docker port on 2376. If you want to configure docker api to expose on port 2375 do the following

* Replace port `2376` with `2375` in below 3 files

  - Vagrantfile
  - config.rb 


* The `config.rb` is used to maintain all the config 

# How to use it 

* [Download](https://www.vagrantup.com/downloads.html) and install vagrant
* Clone this repo `git clone git@github.com:Ankit-Kulkarni/vagrant-configs.git` 
* Move to directory `cd vagrant-configs/xenial`
* Install plugin `vagrant plugin install vagrant-host-shell`
* Run `vagrant up`

