# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.define 'manager' do |manager|
    manager.vm.box = "chef/centos-5.11"
    manager.vm.network 'private_network', ip: '192.168.44.10'
    manager.cache.scope = :box if Vagrant.has_plugin? 'vagrant-cachier'
    manager.vm.provision "shell", inline: <<-SHELL
      sudo hostname manager
      sudo yum install -y epel-release
      sudo yum install --enablerepo=epel -y perl-Config-Tiny perl-Log-Dispatch perl-Parallel-ForkManager
      wget --no-check-certificate https://72003f4c60f5cc941cd1c7d448fc3c99e0aebaa8.googledrive.com/host/0B1lu97m8-haWeHdGWXp0YVVUSlk/mha4mysql-node-0.56-0.el5.noarch.rpm
      sudo rpm -ivh mha4mysql-node-0.56-0.el5.noarch.rpm
      wget --no-check-certificate https://72003f4c60f5cc941cd1c7d448fc3c99e0aebaa8.googledrive.com/host/0B1lu97m8-haWeHdGWXp0YVVUSlk/mha4mysql-manager-0.56-0.el5.noarch.rpm
      sudo rpm -ivh mha4mysql-manager-0.56-0.el5.noarch.rpm
      sudo cp /vagrant/mha.cnf /etc/
    SHELL
  end

  # default db master
  config.vm.define 'db1' do |db1|
    db1.vm.box = "chef/centos-5.11"
    db1.vm.network 'private_network', ip: '192.168.44.20'
    db1.cache.scope = :box if Vagrant.has_plugin? 'vagrant-cachier'
    db1.vm.provision "shell", inline: <<-SHELL
      sudo hostname db1
      sudo cp /vagrant/db1-my.cnf /etc/my.cnf
      sudo yum install -y mysql-server && sudo /etc/init.d/mysqld start
      wget --no-check-certificate https://72003f4c60f5cc941cd1c7d448fc3c99e0aebaa8.googledrive.com/host/0B1lu97m8-haWeHdGWXp0YVVUSlk/mha4mysql-node-0.56-0.el5.noarch.rpm
      sudo rpm -ivh mha4mysql-node-0.56-0.el5.noarch.rpm
      mysql -u root -e "GRANT ALL PRIVILEGES ON *.* TO 'repl'@'192.168.44.0/255.255.255.0' IDENTIFIED BY 'slavepass';"
      mysql -u root -e "CREATE DATABASE repltest;"
      sudo yum install -y epel-release
      sudo wget -O /etc/yum.repos.d/clusterlabs.repo http://clusterlabs.org/rpm/epel-5/clusterlabs.repo
      sudo yum install -y heartbeat-stonith
      sudo yum install -y pacemaker corosync
      sudo mkdir /var/log/cluster/
      sudo corosync-keygen
    SHELL
  end

  config.vm.define 'db2' do |db2|
    db2.vm.box = "chef/centos-5.11"
    db2.vm.network 'private_network', ip: '192.168.44.30'
    db2.cache.scope = :box if Vagrant.has_plugin? 'vagrant-cachier'
    db2.vm.provision "shell", inline: <<-SHELL
      sudo hostname db2
      sudo cp /vagrant/db2-my.cnf /etc/my.cnf
      sudo yum install -y mysql-server && sudo /etc/init.d/mysqld start
      wget --no-check-certificate https://72003f4c60f5cc941cd1c7d448fc3c99e0aebaa8.googledrive.com/host/0B1lu97m8-haWeHdGWXp0YVVUSlk/mha4mysql-node-0.56-0.el5.noarch.rpm
      sudo rpm -ivh mha4mysql-node-0.56-0.el5.noarch.rpm
      mysql -u root -e "GRANT ALL PRIVILEGES ON *.* TO 'repl'@'192.168.44.0/255.255.255.0' IDENTIFIED BY 'slavepass';"
      sudo yum install -y epel-release
      sudo wget -O /etc/yum.repos.d/clusterlabs.repo http://clusterlabs.org/rpm/epel-5/clusterlabs.repo
      sudo yum install -y heartbeat-stonith
      sudo yum install -y pacemaker corosync
      sudo mkdir /var/log/cluster/
    SHELL
  end

  config.vm.define 'db3' do |db3|
    db3.vm.box = "chef/centos-7.0"
    db3.vm.network 'private_network', ip: '192.168.44.40'
    db3.cache.scope = :box if Vagrant.has_plugin? 'vagrant-cachier'
    db3.vm.provision "shell", inline: <<-SHELL
      sudo hostname db3
      sudo cp /vagrant/db3-my.cnf /etc/my.cnf
      sudo yum install -y http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
      sudo yum install --enablerepo=mysql56-community -y mysql-community-server
      sudo systemctl start mysqld
      wget --no-check-certificate https://72003f4c60f5cc941cd1c7d448fc3c99e0aebaa8.googledrive.com/host/0B1lu97m8-haWeHdGWXp0YVVUSlk/mha4mysql-node-0.56-0.el6.noarch.rpm
      sudo rpm -ivh mha4mysql-node-0.56-0.el6.noarch.rpm
      mysql -u root -e "GRANT ALL PRIVILEGES ON *.* TO 'repl'@'192.168.44.0/255.255.255.0' IDENTIFIED BY 'slavepass';"
      sudo yum install -y pacemaker
    SHELL
  end

  # manager, node共通
  config.vm.provision "shell", inline: <<-SHELL
    sudo yum install -y perl-DBD-MySQL
    sudo mkdir -p /root/.ssh/
    sudo cp /vagrant/id_rsa /root/.ssh/
    sudo cp /vagrant/id_rsa.pub /root/.ssh/authorized_keys
    sed -ri 's/#/PermitRootLogin yes/g' /etc/ssh/sshd_config
    sed -ri 's/#RSAAuthentication yes/RSAAuthentication yes/g' /etc/ssh/sshd_config
    sed -ri 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/g'  /etc/ssh/sshd_config
    sed -ri 's/#AuthorizedKeysFile/AuthorizedKeysFile/g'  /etc/ssh/sshd_config
    sudo cp /vagrant/ssh-config ~/.ssh/config
    sudo yum install -y vim-enhanced
  SHELL
end
