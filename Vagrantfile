# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.box = "mvbcoding/awslinux"
  config.vm.network "forwarded_port", guest: 80, host: 8080
  #config.vm.network "private_network", type: "dhcp"
  config.vm.provider "virtualbox" do |v|
    v.memory = 2048
    v.cpus = 1
  end
  config.vm.provision "shell", inline: <<-SHELL
    sudo rm -f /var/run/yum.pid
    #sudo yum -y update
    wget http://dev.mysql.com/get/mysql57-community-release-el6-7.noarch.rpm
    sudo yum -y localinstall mysql57-community-release-el6-7.noarch.rpm
    sudo yum -y install httpd24 php71 php71-mysqlnd php71-mbstring php71-pecl-imagick mysql-community-server
    sudo service mysqld start
    grep -oP -- '(?=root@localhost:\s).*' /var/log/mysqld.log >>  pass.txt
    sed -i 's/root@localhost: //g'  pass.txt
    export DBPWD=$(cat pass.txt)
    sudo cp /vagrant/my.cnf /etc/my.cnf
    sudo service mysqld restart
    mysqladmin -u root -p$DBPWD password 'TimoTest!'
    mysql -uroot -pTimoTest! < /vagrant/create_db.sql
    curl -sS https://getcomposer.org/installer | php
    sudo mv composer.phar /usr/bin/composer
    sudo usermod -a -G apache vagrant 
    sudo chown -R vagrant:apache /var/www
    sudo chmod -R 2775 /var/www
    cd /var/www/html
    sudo cp /vagrant/hosts /etc/hosts
    sudo cp /vagrant/neos.cms.conf /etc/httpd/conf.d/neos.cms.conf
    sudo -u vagrant /usr/bin/composer create-project --no-dev neos/neos-base-distribution neos
    cd /var/www/html/neos
    sudo ./flow core:setfilepermissions root apache apache
    cp /vagrant/Settings.yaml /var/www/html/neos/Configuration/Settings.yaml
    ./flow configuration:validate
    ln -s /vagrant/TS.Example/ /var/www/html/neos/Packages/Sites/
    ./flow flow:package:rescan
    ./flow flow:doctrine:migrate
    ./flow site:import TS.Example
    ./flow user:create admin adm1n Admin User --roles Neos.Neos:Administrator
    sudo service httpd start
  SHELL
end