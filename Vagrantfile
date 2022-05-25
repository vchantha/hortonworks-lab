# -*- mode: ruby -*-
# vi: set ft=ruby :

ENV['VAGRANT_NO_PARALLEL'] = 'yes'

Vagrant.configure(2) do |config|
 
 
config.vm.boot_timeout = 6000 
# Global reset password root
### start inline >>>> 
config.vm.provision "shell", inline: <<-SHELL
cat <<EOF | sudo passwd root
chantha_007
chantha_007
EOF
sudo chmod 777 /etc/ssh/sshd_config
sudo sed -i 's/#PermitRootLogin yes/PermitRootLogin yes/' /etc/ssh/sshd_config
sudo sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config
sudo sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
sudo chmod 644 /etc/ssh/sshd_config
sudo systemctl restart sshd
echo "1.)===> reset password ROOT success <==="
sudo yum update -y
sudo yum groupinstall "Infrastructure Server" --setopt=group_package_types=mandatory,default,optional -y
sudo yum groupinstall "Development Tools" --setopt=group_package_types=mandatory,default,optional -y
echo "2.)===> Update success ! <==="
sudo chmod 777 /etc/hosts
cat <<EOF > /etc/hosts
127.0.0.1       localhost
192.168.56.111  lb1
192.168.56.112  lb2
192.168.56.101  master1
192.168.56.102  master2
192.168.56.201  worker1
192.168.56.202  worker2
192.168.56.203  worker3
EOF
sudo chmod 644 /etc/hosts
echo "3.)===> set hosts success ! <==="
sudo chmod 777 /etc/sysconfig/selinux
sudo sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/sysconfig/selinux
sudo chmod 644 /etc/sysconfig/selinux
echo "4.)===> set selinux success ! <==="
sudo yum install epel-release -y
sudo yum install ntp -y
sudo yum install pssh -y
sudo su - root -c 'yum install pssh -y'
echo "5.)===> Install require package ! <==="
sudo su - root -c 'systemctl start ntpd'
sudo systemctl stop firewalld
sudo systemctl disable firewalld
echo "6.)===> Stopped and Started some service ! <==="
sudo chmod 777 /etc/security/limits.conf
cat <<EOF >> /etc/security/limits.conf
* hard nproc 65535
* soft nproc 65535
* hard nofile 65535
* soft nofile 65535
EOF
sudo chmod 644 /etc/security/limits.conf
echo "7.)===> set limit OS ! <==="
sudo sysctl vm.swappiness=0
sudo chmod 777 /etc/sysctl.conf
echo vm.swappiness=0 >> /etc/sysctl.conf
sudo chmod 644 /etc/sysctl.conf
echo "8.)===> set system control OS ! <==="
cat <<EOF > /etc/yum.repos.d/Ambari-Server.repo
[Ambari]
name=Ambari-Server-Online
enabled=1
baseurl=https://ro-bucharest-repo.bigstepcloud.com/hortonworks-repos/ambari/centos7/2.7.3.0
gpgcheck=0

[HDP]
name=HDP-Online
enabled=1
baseurl=https://ro-bucharest-repo.bigstepcloud.com/hortonworks-repos/HDP/centos7/3.1.0.0
gpgcheck=0

[HDP-GP]
name=HDP-GP-Online
enabled=1
baseurl=https://ro-bucharest-repo.bigstepcloud.com/hortonworks-repos/HDP-GPL/centos7/3.1.0.0
gpgcheck=0

[HDP-UTILS]
name=HDP-UTILS-Online
enabled=1
baseurl=https://ro-bucharest-repo.bigstepcloud.com/hortonworks-repos/HDP-UTILS/centos7/1.1.0.22
gpgcheck=0
EOF
echo "9.)===> add repo ! <==="

SHELL
### end inline <<<< 

### start batch script >>>> 
  ## disable firewall
  #config.vm.provision "shell", path: "system"
### end batch script <<<<

  # Load Balancer Node
#  config.vm.define "lb" do |lb|
#    lb.vm.box = "bento/ubuntu-20.04"
#    lb.vm.hostname = "lb.rean52.com"
#    lb.vm.network "private_network", ip: "192.168.56.111"
#    lb.vm.provider "virtualbox" do |v|
#      v.name = "lb"
#      v.memory = 1024
#      v.cpus = 1
#      v.gui=false
#    end ## end provider
#    lb.vm.provision "shell", path: "setting-disable-firewalld.sh"
#  end ## end define master1

  MasterCount = 1

  (1..MasterCount).each do |i|
    config.vm.define "master#{i}" do |masternode|
      masternode.vm.box = "centos/7"
      masternode.vm.hostname = "master#{i}"
      masternode.vm.network "private_network", ip: "192.168.56.10#{i}"
      masternode.vm.provider "virtualbox" do |v|
        v.name = "master#{i}"
        v.memory = 14000
        v.cpus = 2
        v.gui=false
      end ## end provider
    end ## end loop master
  end ## end define master

  NodeCount = 1

  # Worker Nodes
  (1..NodeCount).each do |i|
    config.vm.define "worker#{i}" do |workernode|
      workernode.vm.box = "centos/7"
      workernode.vm.hostname = "worker#{i}"
      workernode.vm.network "private_network", ip: "192.168.56.20#{i}"
      workernode.vm.provider "virtualbox" do |v|
        v.name = "worker#{i}"
        v.memory = 2096
        v.cpus = 1
        v.gui=false
      end ## end provider
    end ## end loop Worker
  end ## end define Worker

end ## end Vagrant.configure