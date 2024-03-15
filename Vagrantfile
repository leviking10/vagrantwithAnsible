# -*- mode: ruby -*-
# vi: set ft=ruby :

# Configure le niveau de compatibilité de la configuration Vagrant
Vagrant.configure("2") do |config|
   # Configuration globale pour tous les providers VirtualBox utilisés
  config.vm.provider "virtualbox" do |vb|
    # Définit la quantité de mémoire RAM (en Mo) allouée à chaque VM par défaut
    vb.memory = "1024"
  end

  # Configuration de la machine virtuelle "ansible"
  config.vm.define "ansible" do |ansible|
    ansible.vm.box = "bento/ubuntu-20.04"
    ansible.vm.hostname = "ansible"
    # Configure un réseau privé statique
    ansible.vm.network "private_network", type: "static", ip: "192.168.56.10"
    ansible.vm.provider "virtualbox" do |v|
      v.memory = 1024
      v.cpus = 1
      v.name = "Ansible Server"
    end
    # Configuration initiale et installation d'Ansible
    ansible.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update -y
      sudo apt-get install -y software-properties-common
      sudo add-apt-repository --yes --update ppa:ansible/ansible
      sudo apt-get install -y ansible
    SHELL
    # Ajout de serveurs DNS
    ansible.vm.provision "shell", inline: <<-SHELL
      echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf > /dev/null
      echo "nameserver 8.8.4.4" | sudo tee -a /etc/resolv.conf > /dev/null
    SHELL
    # Utilise Ansible pour provisionner davantage la VM avec le playbook spécifié
    ansible.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "setup_web.yml"
    end
  end

  # Configuration de la machine virtuelle "client"
  config.vm.define "client" do |client|
    client.vm.box = "bento/ubuntu-20.04"
    client.vm.hostname = "client"
    # Configure un réseau privé 
    client.vm.network "private_network", ip: "192.168.56.11"
    client.vm.provider "virtualbox" do |v|
      v.memory = 512
      v.cpus = 1
      v.name = "Client"
    end
    # Provisionne la machine client avec un script shell pour installer nmap
    client.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y nmap
    SHELL
  end
end
