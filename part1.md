#Linux setup
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

install maria db

```
