# WP on VM (Praktyki Jesien 2023)



## Wstęp

Projekt przygotowawczy na jesienne praktyki 2023. 


## Założenia

Przygotowanie i automatyzacja infrastruktury pod CMS WordPress z wykorzystaniem narzędzi:

- GIT
- Virtualbox + Vagrant
- Venv
- Ansible
- LEMP
- Wordpress
- Let's Encrypt/Certbot
- Pluginy WP

Finalnie chcemy otrzymać kod który automatycznie postawi nam infrastrukturę kilku instancji WordPress. Instancje powinny być odpowiednio zabezpieczone, uzbrojone w pluginy i system automatycznych backupów. Docelowo infrastruktura będzie hostowana na AWS lub GCP (tbd)

### GIT

#### Tutorial 

- https://medium.com/geekculture/learn-to-use-git-in-10-minutes-53e9a17bf8c2

### Virtualbox

#### Czym jest Virtualbox?

- https://www.virtualbox.org/wiki/VirtualBox

#### Instalacja + Extension Pack

- https://www.virtualbox.org/wiki/Downloads

### Vagrant

#### Czym jest Vagrant?

- https://developer.hashicorp.com/vagrant/intro

#### Instalacja

- https://developer.hashicorp.com/vagrant/downloads

#### Vagrantfile

- https://developer.hashicorp.com/vagrant/docs/vagrantfile

#### Vagrant Boxes

- https://app.vagrantup.com/boxes/search

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

### Venv

- https://docs.python.org/3/library/venv.html

### Ansible

#### Czym jest Ansible?

- https://docs.ansible.com/ansible/latest/index.html

#### Instalacja

- https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-and-upgrading-ansible

#### Pierwsze kroki

- https://docs.ansible.com/ansible/latest/getting_started/index.html

#### Ansible - dokumentacja

- https://docs.ansible.com/

### LEMP

#### Czym jest LEMP?

- https://www.digitalocean.com/community/tutorials/what-is-lemp

#### Instalacja LEMP

- Ubuntu: https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-ubuntu-18-04
- Centos: https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-centos-7

### Wordpress

#### Czym jest Wordpress?

- https://pl.wordpress.org/

#### Instalacja i konfiguracja

- Ubuntu: https://www.digitalocean.com/community/tutorials/install-wordpress-on-ubuntu
- Centos: https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-on-centos-7

### Certyfikaty

#### Czym jest Let's encrypt / Certbot?

- https://letsencrypt.org/getting-started/

#### Dokumentacja

- https://letsencrypt.org/docs/

#### Instalacja i konfiguracja

- https://www.inmotionhosting.com/support/website/ssl/lets-encrypt-ssl-ubuntu-with-certbot/

### Pluginy WP

1. TBD


