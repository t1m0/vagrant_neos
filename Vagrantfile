# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.box = "mvbcoding/awslinux"
  #config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "private_network", type: "dhcp"
  config.vm.provision "shell", inline: <<-SHELL
  	sudo rm -f /var/run/yum.pid
  	sudo yum -y update
  	sudo yum -y install httpd24 php71 php71-mysqlnd php71-mbstring php71-pecl-imagick 
  	curl -sS https://getcomposer.org/installer | php
  	sudo mv composer.phar /usr/bin/composer
  	sudo usermod -a -G apache vagrant 
    sudo chown -R vagrant:apache /var/www
    sudo chmod -R 2775 /var/www
    cd /var/www/html
    sudo -u vagrant /usr/bin/composer create-project --no-dev neos/neos-base-distribution neos
    #cp -r /vagrant/neos /var/www/html/
    sudo cp /vagrant/neos.cms.conf /etc/httpd/conf.d/neos.cms.conf
    cd /var/www/html/neos
    sudo cp /vagrant/hosts /etc/hosts
    sudo ./flow core:setfilepermissions root apache apache
    sudo service httpd start
    ifconfig | grep "inet "
  SHELL
end
