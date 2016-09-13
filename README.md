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
