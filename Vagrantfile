# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Usar el box de Ubuntu 18.04
  config.vm.box = "hashicorp/bionic64"

  # Asignar 512 MB de RAM y 1 CPU a la máquina virtual
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "512"
    vb.cpus = 1
  end

  # Asignar una IP estática a la máquina virtual
  config.vm.network "private_network", type: "dhcp", ip: "192.168.33.10"

  # Compartir un directorio local con el servidor Apache en la VM
  config.vm.synced_folder "./pagina_web", "/var/www/html", create: true

  # Habilitar la instalación de Apache y otros paquetes necesarios para servir una página web
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y apache2
    systemctl enable apache2
    systemctl start apache2
  SHELL
end
