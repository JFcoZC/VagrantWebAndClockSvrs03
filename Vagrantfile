# -*- mode: ruby -*-
# vi: set ft=ruby :

#Definir variable config
Vagrant.configure("2") do |config|
#--- INICIO MAQUINA DEL WEB SERVER ---
  #Configurar para iniciar ssh de servidor usando: vagrant ssh web 
  config.vm.define "web" do |web|
    web.vm.box = "ubuntu/bionic64"
    web.vm.network "forwarded_port", guest: 3000, host: 80
    web.vm.network "private_network", ip: "192.168.0.10"
    web.vm.provision "shell", inline: <<-SHELL
      echo "webserver" > /etc/hostname
      #Confiugrar hostname del servidor
      hostname -b webserver
      #Haciendo que webserver pueda enconctrar al clockserver por nombre
      echo "192.168.0.20        clockserver" >> /etc/hosts
      #Instalar apache
      apt-get update
      apt-get install -y apache2
    SHELL
  end
#--- INICIO MAQUINA DEL CLOCK SERVER ---  
  #Definir variable web
  config.vm.define "clock" do |clock|
    clock.vm.box = "ubuntu/bionic64"


    # Create a forwarded port mapping which allows access to a specific port
    # within the machine from a port on the host machine. In the example below,
    # accessing "localhost:8080" will access port 80 on the guest machine.
    # NOTE: This will enable public access to the opened port
    clock.vm.network "forwarded_port", guest: 3000, host: 3000

    clock.vm.network "private_network", ip: "192.168.0.20"
    clock.vm.provision "shell", inline: <<-SHELL
      echo "clockserver" > /etc/hostname
      #Confiugrar hostname del servidor
      hostname -b clockserver
      #Haciendo que clockserver pueda enconctrar al webserver por nombre
      echo "192.168.0.10        webserver" >> /etc/hosts
      #Instalar Repositorio necesario para insalar node
      sudo apt install curl
      #Descagar repositorio
      curl -sL https://deb.nodesource.com/setup_8.x | sudo bash -
      #Install nodejs and npm del repositorio descargado
      sudo apt install nodejs -y
      #Crear directorio guardar documentos servidor
      mkdir servidor
      #Moverse a carpeta
      cd servidor
      #Clone from github
      sudo apt-get install git
      git clone https://github.com/JFcoZC/Cloud-basedClock.git
      cd Cloud-basedClock/Relojv3/servidor
      #node server.js
    SHELL
  end

end