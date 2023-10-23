# WP on VM (Praktyki Jesien 2023)



## Wstęp

Projekt przygotowawczy na jesienne praktyki 2023. 


## Założenia

Przygotowanie i automatyzacja infrastruktury pod CMS WordPress z wykorzystaniem narzędzi:

- Virtualbox + Vagrant
- Ansible
- LAMP/LEMP
- Wordpress
- Let's Encrypt/Certbot
- Pluginy WP

Finalnie chcemy otrzymać kod który automatycznie postawi nam infrastrukturę kilku instancji WordPress. Instancje powinny być odpowiednio zabezpieczone, uzbrojone w pluginy i system automatycznych backupów. Docelowo infrastruktura będzie hostowana na AWS lub GCP (tbd)

## Część pierwsza - Virtualbox + Vagrant

#### 1. Virtualbox
1.1 Czym jest Virtualbox?
- https://www.virtualbox.org/wiki/VirtualBox

1.2 Instalacja + Extension Pack
- https://www.virtualbox.org/wiki/Downloads

#### 2. Vagrant
1.1 Czym jest Vagrant?
- https://developer.hashicorp.com/vagrant/intro

1.2 Instalacja
- https://developer.hashicorp.com/vagrant/downloads

1.3 Vagrantfile
- https://developer.hashicorp.com/vagrant/docs/vagrantfile

<details>
  <summary>Przykładowy Vagrantfile</summary>
  
  ### Kod:
 ```
 # -*- mode: ruby -*-
# vi: set ft=ruby :

# Define virtualbox boxes
boxes = [
    {
        :name => "srv-ubuntu1",
        :eth1 => "192.168.50.100",
        :mem => "1024",
        :cpu => "1",
        :os => "ubuntu/focal64",
    },
    {
        :name => "srv-ubuntu2",
        :eth1 => "192.168.50.101",
        :mem => "1024",
        :cpu => "1",
        :os => "ubuntu/focal64",
    }
]

# Lets check what kind of SSH key you have generated and upload it on vm
rsa_key = File.expand_path('~') + "/.ssh/id_rsa.pub"
dsa_key = File.expand_path('~') + "/.ssh/id_dsa.pub"

if FileTest.exists?(rsa_key)
  key = rsa_key
elsif  FileTest.exists?(dsa_key)
  key = dsa_key
end

Vagrant.configure(2) do |config|

  boxes.each do |opts|
    config.vm.define opts[:name] do |config|
      config.vm.box = opts[:os]

      #config.ssh.insert_key = false
      ssh_public_key = File.read("#{key}")

      config.vm.network "private_network", ip: opts[:eth1]
      config.vm.hostname = opts[:name]
      config.vm.provider "virtualbox" do |v|
        v.memory = opts[:mem]
        v.cpus = opts[:cpu]
        v.name = opts[:name]
      end

    config.vm.provision "shell", inline: <<-SHELL
        echo "#{ssh_public_key}" >> /home/vagrant/.ssh/authorized_keys
      SHELL

      #config.vm.provision :ansible do |ansible|
      #    ansible.playbook = "pre_provision.yml"
      #    ansible.inventory_path = "development/hosts"
      #    ansible.verbose = "v"
      #end
    end
  end
end
 ```

</details>

## Część druga - Ansible

1. Czym jest Ansible?
- https://docs.ansible.com/ansible/latest/index.html

2. Instalacja
- https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-and-upgrading-ansible

3. Pierwsze kroki
- https://docs.ansible.com/ansible/latest/getting_started/index.html

4. Ansible - dokumentacja
- https://docs.ansible.com/

## Część trzecia - LAMP/LEMP

1. Czym jest LAMP/LEMP?
- https://datasciencedojo.com/blog/lamp-vs-lemp/

2. Instalacja
2.1. Instalacja LAMP
- https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-ubuntu-18-04

2.2. Instalacja LEMP
- https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-ubuntu-18-04




