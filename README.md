![BadzillaVM Logo](http://badzilla.co.uk/sites/default/files/BadzillaVMLogo.png)

## [BadzillaVm](http://badzilla.co.uk/badzillavm)

BadzillaVM is a lightweight VM for Drupal, based heavily on Geerlingguy's DrupalVM, and just like his, built with Ansible.

BadzillaVM is significantly pared down from DrupalVM, and as such it isn't as feature-rich as DrupalVM. It does however offer something DrupalVM does not. 

It offers the ability to run four different version of PHP (web) simmultaneously. In addition, there is a simple mechanism to switch between PHP (cli) versions on the command line. 

Currently, PHP 7.0, 7.1, 7.2 and 7.3 are supported. 

This means that BadzillaVM is useful for agencies that perhaps have multiple clients running Drupal builds on differing versions of PHP. It is a breeze with BadzillaVM to configure multiple virtual hosts, each of which can be assigned any of the available versions of PHP.

BadzillaVM needs Vagrant and VirtualBox to run. You should install those two products first at their latest versions. 

BadzillaVM is a simple VM with lightweight functionality. It isn't intended to support complex Drupal ecosystems. Developers seeking an advanced product set should have a look at DrupalVM instead. 
 
BadzillaVM installs to a Ubuntu 16.04LTS virtual machine. The build contains:

* nginx
* PHP 7.0, 7.1, 7.2, 7.3
* MariaDB Server and Client
* Memcached
* Drush Loader
* Firewall
* Java
* Jenkins
* Mailhog
* Adminer
* Pimpmylog
* Composer
* NodeJS
* XHPRof
* XDebug
* Elasticsearch
* Kibana
* AWS cli
* Platform.sh cli

This is a useful toolset for building professional standard Drupal websites. 

## Installation Guide

### 1 - Install Vagrant and VirtualBox

Download and install [Vagrant](https://www.vagrantup.com/downloads.html) and [VirtualBox](https://www.virtualbox.org/wiki/Downloads).

You can also use an alternative provider like Parallels or VMware. (Parallels Desktop 11+ requires the "Pro" or "Business" edition and the [Parallels Provider](http://parallels.github.io/vagrant-parallels/), and VMware requires the paid [Vagrant VMware integration plugin](http://www.vagrantup.com/vmware)).

Notes:

  - **For faster provisioning** (macOS/Linux only): *[Install Ansible](http://docs.ansible.com/intro_installation.html) on your host machine, so Drupal VM can run the provisioning steps locally instead of inside the VM.*
  - **For stability**: Because every version of VirtualBox introduces changes to networking, for the best stability, you should install Vagrant's `vbguest` plugin: `vagrant plugin install vagrant-vbguest`.
  - **NFS on Linux**: *If NFS is not already installed on your host, you will need to install it to use the default NFS synced folder configuration. See guides for [Debian/Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-ubuntu-14-04), [Arch](https://wiki.archlinux.org/index.php/NFS#Installation), and [RHEL/CentOS](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6).*
  - **Versions**: *Make sure you're running the latest releases of Vagrant, VirtualBox, and Ansible—as of early 2018, BadzillaVM recommends: Vagrant 2.0.x, VirtualBox 5.2.x, and Ansible 2.4.x*
  
### 2 - Build the Virtual Machine

  1. Download this project and put it wherever you want.
  2. Create a file called `config.yml` in the directory in which you downloaded or cloned this repository.
  3. Populate this file with your configuration (see below) *This is mandatory - do not go past this step until you have added your own configuration into this file*
  4. Type in `vagrant up`, and let Vagrant do its magic.
  
### 3 - Configuring the VM

You must configure your VM before you attempt `vagrant up` or the build will error. 

A typical `config.yml` configuration will look similar to this:

    ---
    vagrant_hostname: nigel-dev-box
    
    user_vhosts_conf:
      web1:
        file_name: web1.conf 
        template_name: template-drupal8.conf.j2
        template_params:
          server_name: web1.test      
          root: /var/www/html/web1    
          php_version: 7.3
      meedjum:
        file_name: meedjum.conf 
        template_name: template-drupal8.conf.j2
        template_params:
          server_name: meedjum.test      
          root: /var/www/html/meedjum/docroot   
          php_version: 7.0  
      panalign:
        file_name: panalign.conf 
        template_name: template-drupal8.conf.j2
        template_params:  
          ssl_certificate: /etc/ssl/certs/ssl-cert-snakeoil.pem
          ssl_certificate_key: /etc/ssl/private/ssl-cert-snakeoil.key
          port: "443 ssl"
          server_name: panalign.test      
          root: /var/www/html/panalign/docroot   
          php_version: 7.0          
          
    vagrant_synced_folders:
      - local_path: /Users/nigel/Projects/VHosts
        destination: /var/www/html
        type: nfs
        create: true 
        
You must start the file with a line containing three hyphens.

You should then add a hostname. I've called mine *nigel-dev-box*.

I then define two virtual hosts, named *web1* and *meedjum* respectively. 
*file_name* refers to the file that hold the nginx configuration. The *template_name* can be any defined in `roles/TroodoNmike.nginx-conf/templates`. The *template_params* is a list of the token substitutions which are the *server_name*, *root*, and *php_version*.

Note that my third virtual host is using ssl and I have configured it to use the generated snakeoil certificate that is installed during the Ubuntu installation process.

Finally the synchronise folders must be defined. Ensure you stick to the convention above. The values are self explanatory and they are an array so multiple values can be defined. 

It is not a bad idea to ensure this configuration is saved in a vcs. To that end, I create my `config.yml` file in a sibling directory to BadzillaVM, and simlink it into the BadzillaVM directory. That way it is separate from the BadzillaVM repo, yet Vagrant will find it. 

### 4 - Access the VM.

The dashboard can be accessed by opening clicking through to the [dashboard](http://dashboard.test). To ssh into the VM, simply type `vagrant ssh`.

### 5 - Switching Versions of PHP

All four web versions of PHP run concurrently without any need for interaction. However it is necessary to manually switch between CLI versions of PHP. To do this, whilst inside the VM, simply issue the `switch-php` command. To help you remember which version of PHP you are currently using on the CLI, simply refer to the prompt. It will show something like `PHP 7.0.32-1+ubuntu16.04.1+deb.sury.org+1 (cli) (built: Oct  1 2018 11:45:35) ( NTS )`

## License
This project is licensed under the MIT open source license.

## Credits
[Jeff Geerling](https://www.jeffgeerling.com/)'s Drupal VM was both the inspiration and the starting point for this project. Huge thanks to the incredible work Jeff has done. His amazing book led me to try my own VM build. Please check his book out: [Ansible for DevOps](https://www.ansiblefordevops.com/).

## About the Author
[Badzilla (Nigel Milligan)](http://badzilla.co.uk) is a London UK based Freelance DevOps Drupal Architect / Engineer and Consultant and can be contacted via LinkedIn. 

