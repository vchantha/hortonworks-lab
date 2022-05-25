sudo yum install httpd createrepo -y
service httpd start
systemctl status httpd.service
mkdir -p /var/www/html/repo/
chmod -R 775 /var/www/html/repo

http://master1/repo/HDF/centos7/3.4.1.1-4
https://ro-bucharest-repo.bigstepcloud.com/hortonworks-repos/HDP/centos7/3.1.0.0
https://ro-bucharest-repo.bigstepcloud.com/hortonworks-repos/HDP-UTILS/centos7/1.1.0.22/

1.)-- set password less multi nodes ---
sudo su - root
ssh-keygen -t rsa
chmod 600 $HOME/.ssh/*
ssh-copy-id -i $HOME/.ssh/id_rsa.pub root@master1
ssh-copy-id -i $HOME/.ssh/id_rsa.pub root@worker1
chmod -R 0700 $HOME/.ssh
2.)-- Access multi nodes ---
cat <<EOF > ~/.pssh_hosts_files
root@master1
root@worker1
EOF

pssh -i -h ~/.pssh_hosts_files systemctl enable ntpd 
pssh -i -h ~/.pssh_hosts_files systemctl start ntpd 


sudo systemctl stop firewalld
sudo systemctl disable firewalld

sudo su - root
umask 0022
echo umask 0022 >> /etc/profile
source /etc/profile
pssh -i -h ~/.pssh_hosts_files echo umask 0022 >> /etc/profile

yum groupinstall "Infrastructure Server" --setopt=group_package_types=mandatory,default,optional -y
yum groupinstall "Development Tools" --setopt=group_package_types=mandatory,default,optional -y


mkdir -p /usr/share/java
wget https://repo1.maven.org/maven2/mysql/mysql-connector-java/5.1.49/mysql-connector-java-5.1.49.jar -O /usr/share/java/mysql-connector-java.jar
chmod 777 /usr/share/java/mysql-connector-java.jar

vim /etc/profile
export buildNumber=2.7.3.0.0
source /etc/profile

rpm -ivh https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-community-common-5.7.25-1.el7.x86_64.rpm
rpm -ivh https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-community-libs-5.7.25-1.el7.x86_64.rpm
rpm -ivh https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-community-devel-5.7.25-1.el7.x86_64.rpm
rpm -ivh https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-community-client-5.7.25-1.el7.x86_64.rpm
rpm -ivh https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-community-server-5.7.25-1.el7.x86_64.rpm

https://developpaper.com/how-to-manually-install-mysql-5-7-in-centos7-4/
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-community-common-5.7.25-1.el7.x86_64.rpm
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-community-libs-5.7.25-1.el7.x86_64.rpm
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-community-devel-5.7.25-1.el7.x86_64.rpm
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-community-client-5.7.25-1.el7.x86_64.rpm
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-community-server-5.7.25-1.el7.x86_64.rpm

rpm -ivh mysql-community-server-5.7.25-1.el7.x86_64.rpm --force --nodeps
systemctl start mysqld
# vi /etc/my.cnf
skip-grant-tables=1
systemctl restart mysqld

# yum erase mariadb*
# rpm -ivh mysql-community-common-5.7.25-1.el7.x86_64.rpm
# rpm -ivh mysql-community-libs-5.7.25-1.el7.x86_64.rpm
# rpm -ivh mysql-community-devel-5.7.25-1.el7.x86_64.rpm
# rpm -ivh mysql-community-client-5.7.25-1.el7.x86_64.rpm
mysql -uroot -p

use mysql;
update user set authentication_string=PASSWORD("admin@123") where User='root';
FLUSH PRIVILEGES;
# vi /etc/my.cnf
##skip-grant-tables=1
systemctl restart mysqld


https://stackoverflow.com/questions/43094726/your-password-does-not-satisfy-the-current-policy-requirements

ambari-server setup --jdbc-db=mysql --jdbc-driver=/path/to/jdbc_driver

mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY "admin@123";
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY "!#%TGB4242123%TGB6yhn";
SHOW VARIABLES LIKE 'validate_password%';
mysql -h localhost -u root -p
mysql> SET GLOBAL validate_password.policy=LOW;
mysql>uninstall plugin validate_password;
mysql> create USER 'ambari'@'%' IDENTIFIED BY "bigdata";
mysql> create USER 'hive'@'%' IDENTIFIED BY "bigdata";
GRANT ALL ON *.* TO 'hive'@'%';

mysql> create USER 'oozie'@'%' IDENTIFIED BY "bigdata";
GRANT ALL ON *.* TO 'oozie'@'%';
FLUSH PRIVILEGES;
create database ranger;


create database rangerkms;
mysql> create USER 'rangerkms'@'%' IDENTIFIED BY "bigdata";
GRANT ALL ON *.* TO 'rangerkms'@'%';
FLUSH PRIVILEGES;


create USER 'ranger'@'%' IDENTIFIED BY "bigdata";
GRANT ALL ON *.* TO 'ranger'@'%';
FLUSH PRIVILEGES;


https://community.cloudera.com/t5/Support-Questions/Hive-with-mysql-setup-Check-db-connection-check-was/td-p/146886
ambari-server setup --jdbc-db=mysql --jdbc-driver=/usr/share/java/mysql-connector-java.jar


FLUSH PRIVILEGES;
GRANT ALL ON *.* TO 'ambari'@'%';
mysql> select user,host from mysql.user;
more /etc/my.cnf
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
symbolic-links=0
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid

##skip-grant-tables=1 ## Ignore password require****
skip_ssl
max_allowed_packet=128M
net_read_timeout = 30
net_write_timeout = 60
connect_timeout = 2500
wait_timeout = 28800
interactive_timeout = 28800
ignore-db-dir=lost+found
bind-address=0.0.0.0
expire-logs-days=15
max_connections=2000
innodb_buffer_pool_size= 3G
join_buffer_size= 2M
innodb_log_file_size = 1G



create database ambari;
use ambari;
source /var/lib/ambari-server/resources/Ambari-DDL-MySQL-CREATE.sql


ambari-server install-mpack --mpack=hdf-ambari-mpack-3.4.1.1-4.tar.gz -v


sudo sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/' /etc/ssh/sshd_config
sudo sed -i 's/PasswordAuthentication no/#PasswordAuthentication no/' /etc/ssh/sshd_config
sudo sed -i 's/#GSSAPIKeyExchange no/GSSAPIKeyExchange no/' /etc/ssh/sshd_config


grep PubkeyAuthentication /etc/ssh/sshd_config
grep PasswordAuthentication /etc/ssh/sshd_config
grep GSSAPIKeyExchange /etc/ssh/sshd_config
grep GSSAPICleanupCredentials /etc/ssh/sshd_config

systemctl restart sshd

/usr/lib/ambari-server/web/javascripts/app.js (replace)


admin@123
bigdata
Alast : A!@$$%5tgb2022Bidata%Admin
Knox  : Knox$%5tgb2022Bidata%Admin
Oozie : Oozie%5tgb2022Bidata%Admin
ranger: ranger5tgb2022Bidata%Admin
https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.3.4/bk_Ranger_Install_Guide/content/updating_ranger_admin_passwords.html


KMS   : KMS$$%5tgb2022Bidata%Admin