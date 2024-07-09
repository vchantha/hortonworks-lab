fix: druid
ALTER DATABASE dbname CHARACTER SET utf8 COLLATE utf8_general_ci;

https://cloudxlab.com/blog/install-hdp-on-aws/

IAM: ami-0f734c7a42620a83d

master : i-069727880e706c54e | 52.221.252.236 | 172.31.32.91 | ec2-52-221-252-236.ap-southeast-1.compute.amazonaws.com  | 

sudo sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
hostname -f
sudo systemctl disable firewalld
sudo service firewalld stop
sudo setenforce 0 ### For current session
sudo vi /etc/selinux/config
SELINUX=disabled ### Permanent

sudo vi /etc/profile
umask 0022

sudo systemctl disable chronyd.service
sudo yum install -y ntp && sudo systemctl start ntpd && sudo systemctl enable ntpd
sudo systemctl disable chronyd.service

---mysql 
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


cat <<EOF  | sudo tee /etc/yum.repos.d/ambari.repo
## Ambari ==
[ambari-2.7.3]
name=ambari Version - ambari-2.7.3
baseurl=https://ro-bucharest-repo.bigstepcloud.com/hortonworks-repos/ambari/centos7/2.7.3.0
gpgcheck=0
enabled=1
## HDP ==
[HDP-3.1.0]
name=HDP Version -3.1.0
baseurl=https://ro-bucharest-repo.bigstepcloud.com/hortonworks-repos/HDP/centos7/3.1.0.0
gpgcheck=0
enabled=1
## UTILs ==
[HDP-UTILS-1.1.0]
name=HDP-UTILS- Version -1.1.0.22
baseurl=https://ro-bucharest-repo.bigstepcloud.com/hortonworks-repos/HDP-UTILS/centos7/1.1.0.22
gpgcheck=0
enabled=1
EOF


mysql> CREATE USER 'ambari'@'localhost' IDENTIFIED BY 'bigdata'; 
mysql> GRANT ALL ON *.* TO 'ambari'@'localhost';
mysql> grant ALL privileges on *.* to 'ambari'@'localhost' with grant option;
mysql> CREATE USER 'ambari'@'%' IDENTIFIED BY 'bigdata'; 
mysql> GRANT ALL ON *.* TO 'ambari'@'%';
mysql> grant ALL privileges on *.* to 'ambari'@'%' with grant option;
mysql> FLUSH PRIVILEGES;
mysql> select @@datadir;

CREATE DATABASE ambari CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE DATABASE ranger CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;



https://computingforgeeks.com/how-to-install-postgresql-13-on-centos-7/
sudo yum install https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm -y
yum -y install postgresql13 postgresql13-server
/usr/pgsql-13/bin/postgresql-13-setup initdb
systemctl enable --now postgresql-13
vi /var/lib/pgsql/13/data/postgresql.conf
listen_addresses = '*'
vi /var/lib/pgsql/13/data/pg_hba.conf
host all all 0.0.0.0/0 md5
# su - postgres -c "psql"
# CREATE ROLE ambari WITH SUPERUSER LOGIN PASSWORD  'bigdata';
ambari=# \i /var/lib/ambari-server/resources/Ambari-DDL-Postgres-CREATE.sql

SETDM2mQwnj8ZzF
bigdata

master: 172.31.32.91 -ambari : 172.31.35.224 : ip-172-31-32-91.ap-southeast-1.compute.internal	-namenode
node1 : 172.31.34.9  : ip-172-31-34-9.ap-southeast-1.compute.internal	
node2 : 172.31.35.61 : ip-172-31-35-61.ap-southeast-1.compute.internal	
node3 : 172.31.34.215 : ip-172-31-34-215.ap-southeast-1.compute.internal -snamenode	
node4 : 172.31.35.224 : ip-172-31-35-224.ap-southeast-1.compute.internal	


Error executing: CREATE TABLE `ranger_masterkey` ( `id` bigint( 20 ) NOT NULL AUTO_INCREMENT , `create_time` datetime DEFAULT NULL , `update_time` datetime DEFAULT NULL , `added_by_id` bigint( 20 ) DEFAULT NULL , `upd_by_id` bigint( 20 ) DEFAULT NULL , `cipher` varchar( 255 ) DEFAULT NULL UNIQUE, `bitlength` int DEFAULT NULL UNIQUE, `masterkey` varchar(2048), PRIMARY KEY ( `id` ) )ROW_FORMAT=DYNAMIC; 
com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Specified key was too long; max key length is 767 bytes
SQLException : SQL state: 42000 com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Specified key was too long; max key length is 767 bytes ErrorCode: 1071
2024-07-09 06:03:01,226  [E] kms_core_db.sql DB schema import failed!

[[ALTER TABLE `mytable` ADD UNIQUE ( column1(15), column2(200) );

https://kylo.readthedocs.io/en/v0.8.2/installation/KerberosInstallationExampleHDP2.4.html
