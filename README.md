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
