# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  BOX = "ubuntu/jammy64"

  # --- CONTROL ---
  config.vm.define "control" do |c|
    c.vm.box = BOX
    c.vm.hostname = "control.local"
    c.vm.network "private_network", ip: "192.168.10.1"
    c.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
      vb.cpus   = 2
    end

    # Instalar Ansible dentro de la VM control
    c.vm.provision "shell", inline: <<-SHELL
      apt-get update -y
      apt-get install -y software-properties-common
      apt-add-repository --yes --update ppa:ansible/ansible
      apt-get install -y ansible git
    SHELL

    # Compartir el directorio del proyecto para ejecutar ansible desde control
    c.vm.synced_folder ".", "/vagrant", type: "virtualbox"
  end

  # --- SERVER ---
  config.vm.define "server" do |s|
    s.vm.box = BOX
    s.vm.hostname = "server.local"
    s.vm.network "private_network", ip: "192.168.10.2"
    s.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
      vb.cpus   = 2
    end
  end

  # --- MONITOR ---
  config.vm.define "monitor" do |m|
    m.vm.box = BOX
    m.vm.hostname = "monitor.local"
    m.vm.network "private_network", ip: "192.168.10.3"
    m.vm.provider "virtualbox" do |vb|
      vb.memory = 2048
      vb.cpus   = 2
    end
  end
end
