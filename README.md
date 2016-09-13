# ClouderaCluster

## 1. Before Installation
Before installation configure SSH, iptables and hosts files.

### 1.1 Hosts files and hostname

```
$ sudo vi /etc/hosts

127.0.0.1	localhost
127.0.0.1 localdomain localhost

192.168.11.132 ubuservmaster
192.168.11.133 ubuservslave1
192.168.11.130 ubuservslave2
192.168.11.134 ubuservslave3

$ sudo vi /etc/hostname
ubuservslave1

```

### 1.2 Configure iptables

```
$ sudo iptables -A INPUT -s 192.168.11.0/24 -d 192.168.11.0/24 -j ACCEPT
$ sudo iptables -A OUTPUT -s 192.168.11.0/24 -d 192.168.11.0/24 -j ACCEPT

$ sudo service ufw restart
```

### 1.3 Configure root access

```
$ sudo passwd root
$ sudo passwd -u root
```

Install openssh-server and allow root login:

```
$ sudo apt install openssh-server
$ sudo vi /etc/ssh/sshd_config
PermitRootLogin yes
$ sudo service ssh restart
```

### 1.4 Optimize the usage of Swap

Temporarily change swappiness’ value to 10 using following command, and it will be reverted in next restart.

```
$ sudo sysctl vm.swappiness=10
```
To permanently change this value, using:

```
$ sudo vi /etc/sysctl.conf
```
Search for vm.swappiness and change its value as desired. If vm.swappiness does not exist, add it to the end of the file like so:

```
vm.swappiness=10
```

Check the value of swappiness:

```
$ cat /proc/sys/vm/swappiness
```

### 1.5 Install and configure MySQL 5.7

Install mysql server: 

```
$ wget http://dev.mysql.com/get/mysql-apt-config_0.6.0-1_all.deb
$ sudo dpkg -i mysql-apt-config_0.6.0-1_all.deb
$ sudo apt-get update
$ sudo apt-get install mysql-server
```

Configure mysql server:

```
$ sudo service mysql stop
$ sudo mv /var/lib/mysql/ib_logfile0 ~/
$ sudo mv /var/lib/mysql/ib_logfile1 ~/

$ sudo vi /etc/mysql/my.cnf
[mysqld]
transaction-isolation = READ-COMMITTED
# Disabling symbolic-links is recommended to prevent assorted security risks;
# to do so, uncomment this line:
# symbolic-links = 0

#key_buffer = 16M
key_buffer_size = 32M
max_allowed_packet = 32M
thread_stack = 256K
thread_cache_size = 64
query_cache_limit = 8M
query_cache_size = 64M
query_cache_type = 1

max_connections = 550
#expire_logs_days = 10
#max_binlog_size = 100M

#log_bin should be on a disk with enough free space. Replace '/var/lib/mysql/mysql_binary_log' with an appropriate path for your system
#and chown the specified folder to the mysql user.
log_bin=/var/lib/mysql/mysql_binary_log
server-id=1
# For MySQL version 5.1.8 or later. Comment out binlog_format for older versions.
binlog_format = mixed

read_buffer_size = 2M
read_rnd_buffer_size = 16M
sort_buffer_size = 8M
join_buffer_size = 8M

# InnoDB settings
innodb_file_per_table = 1
innodb_flush_log_at_trx_commit  = 2
innodb_log_buffer_size = 64M
innodb_buffer_pool_size = 4G
innodb_thread_concurrency = 8
innodb_flush_method = O_DIRECT
innodb_log_file_size = 512M

[mysqld_safe]
log-error=/var/log/mysql/error.log
pid-file=/var/run/mysqld/mysqld.pid

sql_mode=STRICT_ALL_TABLES
```

Start the MySQL server:

```
$ sudo service mysql start
```
Install MySQL JDBC Driver

```
$ sudo apt-get install libmysql-java
```

Create users and databases in MySQL:

```
$ mysql -u root -p
Enter password:

mysql> create database cloudera DEFAULT CHARACTER SET utf8;
mysql> grant all on cloudera.* TO 'akmal'@'%' IDENTIFIED BY 'Goldfish';

mysql> create database hive DEFAULT CHARACTER SET utf8;
mysql> grant all on hive.* TO 'akmal'@'%' IDENTIFIED BY 'Goldfish';

mysql> create database hue DEFAULT CHARACTER SET utf8;
mysql> grant all on hue.* TO 'akmal'@'%' IDENTIFIED BY 'Goldfish';

mysql> create database oozie DEFAULT CHARACTER SET utf8;
mysql> grant all on oozie.* TO 'akmal'@'%' IDENTIFIED BY 'Goldfish';
```

## 2. Establish Your Cloudera Manager Repository Strategy

```
$ wget https://archive.cloudera.com/cm5/ubuntu/trusty/amd64/cm/cloudera.list
$ sudo cp cloudera.list /etc/apt/sources.list.d/cloudera-manager.list
$ sudo apt-get update
```

## 3. Install Cloudera Manager Server Software

```
$ sudo apt-get install oracle-j2sdk1.7
$ sudo apt-get install cloudera-manager-daemons cloudera-manager-server
$ sudo service cloudera-scm-server start
```

### 3.1 Preparing a Cloudera Manager Server External Database

```
$ sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql cloudera akmal Goldfish
```

Start the Cloudera Manager Server:
```
$ sudo service cloudera-scm-server start
```

To observe the startup process, run:
```
$ sudo tail -f /var/log/cloudera-scm-server/cloudera-scm-server.log 
```
on the Cloudera Manager Server host.

Go to http://ubuservmaster:7180
