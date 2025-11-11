# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  provider = ENV["VAGRANT_DEFAULT_PROVIDER"] || ENV["VAGRANT_PROVIDER"] || "virtualbox"

  BOXES = {
    "virtualbox" => "ubuntu/jammy64",     # Oficial Ubuntu: trae provider virtualbox
    "libvirt"    => "generic/ubuntu2204", # Box multi-provider que sí trae libvirt
  }

  box = BOXES.fetch(provider) { "ubuntu/jammy64" }

  # -------- CONTROL --------
  config.vm.define "control" do |c|
    c.vm.box = box
    c.vm.hostname = "control.local"
    c.vm.network "private_network", ip: "192.168.10.10"

    c.vm.provider "virtualbox" do |vb|
      vb.memory = 1024; vb.cpus = 2; vb.name = "DE-control"
    end
    c.vm.provider "libvirt" do |lv|
      lv.memory = 1024; lv.cpus = 2; lv.title = "DE-control"
    end
  end

  # -------- SERVER --------
  config.vm.define "server" do |s|
    s.vm.box = box
    s.vm.hostname = "server.local"
    s.vm.network "private_network", ip: "192.168.10.11"

    s.vm.provider "virtualbox" do |vb|
      vb.memory = 2048; vb.cpus = 2; vb.name = "DE-server"
    end
    s.vm.provider "libvirt" do |lv|
      lv.memory = 2048; lv.cpus = 2; lv.title = "DE-server"
    end
  end

  # -------- MONITOR --------
  config.vm.define "monitor" do |m|
    m.vm.box = box
    m.vm.hostname = "monitor.local"
    m.vm.network "private_network", ip: "192.168.10.12"

    m.vm.provider "virtualbox" do |vb|
      vb.memory = 3072; vb.cpus = 2; vb.name = "DE-monitor"
    end
    m.vm.provider "libvirt" do |lv|
      lv.memory = 3072; lv.cpus = 2; lv.title = "DE-monitor"
    end
  end
end
