# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # ===================================
  # SERVIDOR iVentoy
  # ===================================
  config.vm.define "iventoy" do |srv|
    srv.vm.box = "ubuntu/jammy64"
    srv.vm.hostname = "iventoy-server"

    # Forcem la connexió a la mateixa xarxa interna ("vboxnet0") que el client.
    srv.vm.network "private_network", ip: "192.168.56.10", virtualbox__intnet: "vboxnet0"

    # Redirecció de port per poder accedir a la interfície web des del nostre ordinador (Host).
    srv.vm.network "forwarded_port", guest: 26000, host: 26000

    srv.vm.provider "virtualbox" do |vb|
      vb.name = "iventoy-server"
      vb.memory = 2048
      vb.cpus = 2
    end

    # Munta la carpeta local "iventoy" dins de la VM amb permisos de root per evitar problemes.
    srv.vm.synced_folder "./iventoy", "/opt/iventoy", owner: "root", group: "root"

    # Provisionament: instal·la paquets necessaris i inicia iVentoy.
    srv.vm.provision "shell", inline: <<-SHELL
      set -e
      sudo apt-get update
      sudo apt-get install -y net-tools
      cd /opt/iventoy
      chmod +x iventoy.sh
      # El servei s'inicia, però l'activació del PXE s'ha de fer manualment des de la web.
      sudo ./iventoy.sh start
    SHELL
  end

  # ===================================
  # CLIENT PXE
  # ===================================
  config.vm.define "pxeclient" do |cli|
    cli.vm.box = "ubuntu/jammy64"
    cli.vm.hostname = "pxe-client"
    
    # Connecta el client a la mateixa xarxa "vboxnet0", sense IP configurada per forçar-lo a demanar-ne una per DHCP.
    cli.vm.network "private_network", virtualbox__intnet: "vboxnet0", auto_config: false

    cli.vm.provider "virtualbox" do |vb|
      vb.name = "pxe-client"
      vb.memory = 1024
      vb.cpus = 1
      
      # 1. Configura l'ordre d'arrencada: primer per xarxa (net).
      vb.customize ["modifyvm", :id, "--boot1", "net", "--boot2", "disk", "--boot3", "none", "--boot4", "none"]
      
      # 2. LÍNIA CLAU: Assigna la prioritat d'arrencada més alta a l'Adaptador de Xarxa 2 (la nostra xarxa privada).
      # Vagrant crea l'Adaptador 1 per NAT i el 2 per a la xarxa privada.
      # Amb això, ens assegurem que l'arrencada PXE es fa per la xarxa correcta.
      vb.customize ["modifyvm", :id, "--nicbootprio2", "1"]
    end
  end
end
