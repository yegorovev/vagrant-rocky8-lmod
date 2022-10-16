# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.box = "bento/rockylinux-8"
  config.vm.box_check_update = false
  config.vm.provider "virtualbox" do |vb|
    vb.gui = true
    vb.memory = "2048"
    vb.cpus = 2
    vb.name = "rockylinux8-lmod"
  end
  
  config.vm.provision "shell", inline: <<-SHELL
    dnf -y install epel-release
    dnf config-manager --set-enabled powertools
    dnf -y install gcc
    dnf -y install lua
    dnf -y install lua5.1-bitop
    dnf -y install lua-devel
    dnf -y install lua-json
    dnf -y install lua-lpeg
    dnf -y install lua-posix
    dnf -y install lua-term
    dnf -y install tcl
    dnf -y install tcl-devel
    wget https://github.com/TACC/Lmod/archive/refs/tags/8.7.13.tar.gz
    tar xzf 8.7.13.tar.gz
    cd Lmod-8.7.13/
    ./configure --prefix=/opt/apps
    make install
    ln -s /opt/apps/lmod/lmod/init/profile /etc/profile.d/z00_lmod.sh
    ln -s /opt/apps/lmod/lmod/init/cshrc   /etc/profile.d/z00_lmod.csh
  SHELL
end
