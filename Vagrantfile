# -*- mode: ruby -*-
# vi: set ft=ruby :

#Definir variable config
Vagrant.configure("2") do |config|
#--- INICIO MAQUINA DEL WEB SERVER ---
  #Configurar para iniciar ssh de servidor usando: vagrant ssh web 
  config.vm.define "web" do |web|
    web.vm.box = "ubuntu/bionic64"
    #Permitir que todo trafico mandado al puerto del host (computadora) sea mandado al puerto
    #del guest(maquina virtual). El Daemon se pone en el puerto de la computadora para redireccionar
    #el trafico
    web.vm.network "forwarded_port", guest: 80, host: 80
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
      #Crear carpetas requeridas
      sudo mkdir -p /var/www/example.com/html
      #Asignar el usuario propietario del directorio
      sudo chown -R $USER:$USER /var/www/example.com/html
      #Asegurarse de que el directorio raiz para la web tiene los permisos necesarios(puede omitirse)
      sudo chmod -R 755 /var/www/example.com
      #Moverse a directorio
      cd /var/www/html
      #Bajar documentos github (Clone)
      sudo apt-get install git
      git clone https://github.com/JFcoZC/Cloud-basedClock.git
      wget https://github.com/JFcoZC/Cloud-basedClock.git/blob/master/jquery-3.3.1.js
      #Eliminar archivos de mas
      cd Cloud-basedClock/Relojv3
      rm -rf servidor

    SHELL
  end
#--- INICIO MAQUINA DEL CLOCK SERVER ---  
  #Definir variable web
  config.vm.define "clock" do |clock|
    clock.vm.box = "ubuntu/bionic64"


    # Create a forwarded port mapping which allows access to a specific port
    # within the machine from a port on the host machine.
    #Permitir que todo trafico mandado al puerto del host (computadora) sea mandado al puerto
    #del guest(maquina virtual). El Daemon se pone en el puerto de la computadora para redireccionar
    #el trafico hacia la maquina virtual
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
      #Generar el paquete de json requerido por express
      npm init -y
      #Install express and save flag to update dependencies inside package.json
      npm install express@4.16.0 --save
      #Descargar con wget archivo js CRUDO del server y guardarlo como server.js
      wget -O server.js https://raw.githubusercontent.com/JFcoZC/Cloud-basedClock/master/Relojv3/servidor/server.js
      #Instalar cors library requerida por node
      npm install cors
      #Levantar servidor del micro servicio en puerto 3000
      node server.js
    SHELL
  end

end