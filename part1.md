# Linux setup

```
i. Add the following linux accounts to all nodes (all host)
$sudo useradd training -u 3800
$sudo passwd training
$sudo groupadd skcc
$sudo usermod -a -G skcc training
$sudo usermod -aG wheel training

ii. List the your instances by IP address and DNS name (don’t use /etc/hosts
for this) (all host)
$sudo hostnamectl set-hostname m1.node.com
$sudo hostnamectl set-hostname cm.node.com
$sudo hostnamectl set-hostname d1.node.com
$sudo hostnamectl set-hostname d2.node.com
$sudo hostnamectl set-hostname d3.node.com
$sudo reboot

set password for user centos (SSH 접근 위해) (all host)
$sudo passwd centos
change the property of ssh for access another host
$sudo vi /etc/ssh/sshd_config
change -> PasswordAuthentication yes

iii. List the Linux release you are using
$cat /etc/redhat-release

iv. List the file system capacity for the first node (master node)
$df -h

v. List the command and output for yum repolist enabled
Configure the repository for CM 5.15.2
  $ sudo yum install -y wget
  $ sudo wget https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo -P /etc/yum.repos.d/

  $ sudo vi /etc/yum.repos.d/cloudera-manager.repo
  edit baseurl
  baseurl=https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/5.15.2/
  sudo rpm --import \
https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/RPM-GPG-KEY-cloudera

  verify yum replist
  $yum repolist
  
vi. List the /etc/passwd entries for training (only in master name node)
$getent passwd training

vii. List the /etc/group entries for skcc (only in master name node)
$ getent group skcc

viii. List output of the flowing commands:
1. getent group skcc
2. getent passwd training

```
# Install a MySQl server
```
install java8 (all host)
$sudo yum install -y java-1.8.0-openjdk-devel.x86_64

1. CM 5.15.2 Repo 설정 (모든 host)
 sudo yum install -y wget
 sudo wget https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo -P /etc/yum.repos.d/
 sudo vi /etc/yum.repos.d/cloudera-manager.repo 편집하여 아래내용 변경
 baseurl=https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/5.15.2/

2. JDK, JDBC 설치(모든 host)
 1) jdk -> sudo yum install java-1.8.0-openjdk-devel.x86_64
 2) jdbc -> 
    wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.47.tar.gz
    tar zxvf mysql-connector-java-5.1.47.tar.gz
    sudo mkdir -p /usr/share/java/
    cd mysql-connector-java-5.1.47
    sudo cp mysql-connector-java-5.1.47-bin.jar /usr/share/java/mysql-connector-java.jar
    
 3. yum Install CM (CM host)
   sudo yum install -y cloudera-manager-daemons cloudera-manager-server
  
 4. Install MySQL server (CM host)
   sudo yum install -y mariadb-server
   sudo systemctl enable mariadb (안되면 재설치)
   sudo systemctl start mariadb
   sudo /usr/bin/mysql_secure_installation
   
   [...]
Enter current password for root (enter for none):
OK, successfully used password, moving on...
[...]
Set root password? [Y/n] Y
New password:
Re-enter new password:
[...]
Remove anonymous users? [Y/n] Y
[...]
Disallow root login remotely? [Y/n] N
[...]
Remove test database and access to it [Y/n] Y
[...]
Reload privilege tables now? [Y/n] Y
[...]
All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!

   
 5. MySQL DB생성 및 권한부여 (CM host)
 mysql -u root -p --> db 접속
 
  CREATE DATABASE scm DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
  CREATE DATABASE amon DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
  CREATE DATABASE rman DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
  CREATE DATABASE hue DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
  CREATE DATABASE metastore DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
  CREATE DATABASE sentry DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
  CREATE DATABASE nav DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
  CREATE DATABASE navms DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
  CREATE DATABASE oozie DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;

  GRANT ALL ON scm.* TO 'scm'@'%' IDENTIFIED BY 'password';
  GRANT ALL ON amon.* TO 'amon'@'%' IDENTIFIED BY 'password';
  GRANT ALL ON rman.* TO 'rman'@'%' IDENTIFIED BY 'password';
  GRANT ALL ON hue.* TO 'hue'@'%' IDENTIFIED BY 'password';
  GRANT ALL ON metastore.* TO 'hive'@'%' IDENTIFIED BY 'password';
  GRANT ALL ON sentry.* TO 'sentry'@'%' IDENTIFIED BY 'password';
  GRANT ALL ON nav.* TO 'nav'@'%' IDENTIFIED BY 'password';
  GRANT ALL ON navms.* TO 'navms'@'%' IDENTIFIED BY 'password';
  GRANT ALL ON oozie.* TO 'oozie'@'%' IDENTIFIED BY 'password';

  SHOW DATABASES;
 
  EXIT;
  
  6. cloudera manager 설치 (CM Host)
sudo yum install cloudera-manager-daemons cloudera-manager-server
7. cloudera manager db 설정 (CM Host)
sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql scm scm password
db.mgmt.properries 존재한다면
sudo rm /etc/cloudera-scm-server/db.mgmt.properties
8. cloudera manager 실행 (CM Host)
sudo systemctl start cloudera-scm-server
sudo tail -f /var/log/cloudera-scm-server/cloudera-scm-server.log --> 로그 확인
```
