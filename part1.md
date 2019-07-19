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
```
![photo.PNG](https://github.com/joo-hwan/SKCC_final_JooHwan/blob/master/part1/hosts.PNG?raw=true)
```
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

install the cluster
access the cm UI admin / admin

set up the hosts
m1, cm, d1, d2, d3

verify parcel cm version 5.15.2

hadoop core 로 먼저 설치

impala 설치 후 sqoop 설치

설치 완료 화면

hue 화면


```
# In MySQL create the sample tables that will be used for the rest of the test
```
$ mysql -u root -p

create database
CREATE DATABASE test;
EXIT;

git bash 사용해 zip 파일 가져오기
$scp posts.sql.zip training@13.124.203.61:.
$scp authors.sql.zip training@13.124.203.61:.

training 계정 접속
zip파일 해제 위해 unzip 설치
$sudo yum install -y unzip
$unzip posts.sql.zip
$unzip authors.sql.zip

table 생성 위해 아래 명령어
$mysql -u root -p test < ./authors-23-04-2019-02-34-beta.sql
$mysql -u root -p test < ./posts23-04-2019\ 02-44.sql

training 계정 권한 주기
$ mysql -u root -p
GRANT ALL ON test.* TO 'training'@'%' IDENTIFIED BY 'training';
GRANT ALL ON test.* TO 'training'@'localhost' IDENTIFIED BY 'training';
FLUSH PRIVILEGES;

training 계정 권한 확인
$ mysql -u training -p

show databases;
use test;
show tables;

desc authors;
desc posts;

select count(*) from authors;
select count(*) from posts;

select * from authors limit 10;
select * from posts limit 10;
```
# Extract tables authors and posts from the database and create Hive tables.
```
$sqoop import --connect jdbc:mysql://172.31.36.41:3306/test --username training --password training --table authors --target-dir /user/training/authors --hive-import --create-hive-table --hive-table default.authors --input-fields-terminated-by '\t'

$sqoop import --connect jdbc:mysql://172.31.36.41:3306/test --username training --password training --table posts --target-dir /user/training/posts --hive-import --create-hive-table --hive-table default.posts --input-fields-terminated-by '\t'

author 테이블 external로 변경
Alter table default.authors SET TBLPROPERTIES('EXTERNAL'='TRUE')

```
# Create and run a Hive/Impala query. From the query, generate the results dataset that you will use in the next step to export in MySQL.
```
INSERT OVERWRITE DIRECTORY '/user/training/results'
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t'
STORED AS TEXTFILE
select a.id as Id, a.first_name as fname,a.last_name as Lname, b.cnt as num_posts
from default.authors a, 
(select author_id, count(*) as cnt from default.posts group by author_id) b
where a.id = b.author_id
;

CREATE TABLE results (id int NOT NULL, fname varchar(255) default NULL, Lname varchar(255) default NULL, num_posts int default 0 );

sqoop export --connect jdbc:mysql://172.31.36.41:3306/test --username training --password training --table results --export-dir /user/training/results --input-fields-terminated-by '\t'

select * from results limit 10;
```
