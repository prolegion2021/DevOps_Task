### Linux Networking

Vagrant file in directory `vagrant-net-static` consist network virtualisation
1 server with 2 clients.
In first part of task setup static ip's and check of it's ping.

requirements
VirtualBox 6.1
Vagrant 2.2.18 or higher

vagrant up #start vm's
vagrant ssh server #connect to server with ssh by key
vagrant halt #poweroff vm's

```
client1:~$ uname -a
Linux client1 4.14.167-0-virt #1-Alpine SMP Thu Jan 23 10:58:18 UTC 2020 x86_64 Linuclient1:~$ uname -a
Linux client1 4.14.167-0-virt #1-Alpine SMP Thu Jan 23 10:58:18 UTC 2020 x86_64 Linux
client1:~$ ping 10.92.17.1
PING 10.92.17.1 (10.92.17.1): 56 data bytes
64 bytes from 10.92.17.1: seq=0 ttl=42 time=0.426 ms
64 bytes from 10.92.17.1: seq=1 ttl=42 time=0.519 ms
64 bytes from 10.92.17.1: seq=2 ttl=42 time=0.669 ms
^C
--- 10.92.17.1 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.426/0.538/0.669 ms
client1:~$ ping 10.92.17.2
PING 10.92.17.2 (10.92.17.2): 56 data bytes
64 bytes from 10.92.17.2: seq=0 ttl=42 time=0.426 ms
64 bytes from 10.92.17.2: seq=1 ttl=42 time=0.207 ms
^C
--- 10.92.17.2 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 0.207/0.316/0.426 ms
client1:~$ ifconfig
eth0      Link encap:Ethernet  HWaddr 08:00:27:1B:23:B0
          inet addr:10.0.2.15  Bcast:0.0.0.0  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe1b:23b0/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:1345 errors:0 dropped:0 overruns:0 frame:0
          TX packets:930 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:156577 (152.9 KiB)  TX bytes:154483 (150.8 KiB)

eth1      Link encap:Ethernet  HWaddr 08:00:27:47:7B:BB
          inet addr:10.92.17.2  Bcast:0.0.0.0  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe47:7bbb/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:10 errors:0 dropped:0 overruns:0 frame:0
          TX packets:25 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:828 (828.0 B)  TX bytes:2180 (2.1 KiB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:8 errors:0 dropped:0 overruns:0 frame:0
          TX packets:8 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:672 (672.0 B)  TX bytes:672 (672.0 B)
```

From server to client 1:

```
server:~$ traceroute 10.92.17.2
traceroute to 10.92.17.2 (10.92.17.2), 30 hops max, 46 byte packets
 1  *  *  10.92.17.2 (10.92.17.2)  1.101 ms
server:~$ 
```
From client2 to client 1:

```
client2:~$ ping 172.16.17.1
PING 172.16.17.1 (172.16.17.1): 56 data bytes
64 bytes from 172.16.17.1: seq=0 ttl=42 time=2.375 ms
64 bytes from 172.16.17.1: seq=1 ttl=42 time=0.620 ms
^C
--- 172.16.17.1 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 0.620/1.497/2.375 ms
client2:~$ traceroute 172.16.17.1
traceroute to 172.16.17.1 (172.16.17.1), 30 hops max, 46 byte packets
 1  172.16.17.1 (172.16.17.1)  0.096 ms  0.070 ms  0.060 ms
client2:~$
```

By manually it's can realise by modifying /etc/network/interfaces
```
iface eth0 inet static
        address 172.16.17.1
        netmask 255.255.255.0
```	

__DHCP__

```
client1:~$ ifconfig eth1
eth1      Link encap:Ethernet  HWaddr 08:00:27:65:5A:04
          inet addr:10.5.92.2  Bcast:0.0.0.0  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe65:5a04/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:7 errors:0 dropped:0 overruns:0 frame:0
          TX packets:15 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:986 (986.0 B)  TX bytes:1662 (1.6 KiB)

client1:~$ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.2.2        0.0.0.0         UG    202    0        0 eth0
0.0.0.0         10.5.92.1       0.0.0.0         UG    203    0        0 eth1
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.5.92.0       0.0.0.0         255.255.255.0   U     0      0        0 eth1
10.5.92.1       0.0.0.0         255.255.255.255 UH    0      0        0 eth1
172.16.17.0      0.0.0.0         255.255.255.0   U     0      0        0 eth2
client1:~$ traceroute i.ua
traceroute to i.ua (104.18.2.81), 30 hops max, 46 byte packets
 1  10.0.2.2 (10.0.2.2)  0.119 ms  0.145 ms  0.148 ms
 2  10.0.2.1 (10.0.2.1)  3.338 ms  3.244 ms  5.205 ms
 3  77.120.39.225.lvv.volia.net (77.120.39.225)  3.197 ms  6.937 ms  6.711 ms

```
```
client1:~$ ping 10.5.92.1
PING 10.5.92.1 (10.5.92.1): 56 data bytes
64 bytes from 10.5.92.1: seq=0 ttl=42 time=1.852 ms
64 bytes from 10.5.92.1: seq=1 ttl=42 time=1.553 ms
^C
--- 10.5.92.1 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 1.553/1.702/1.852 ms
```

settings for setup in Vagrant file
in section client1

In the task5 I learn how to use route and ip, setuppping permanent setting for static routes and configuring some manual manipulating with dev interfaces.
There are many ways to do this in others distributives like Debian/Ubuntu/CentOS.

###################################

File `vagrant-net/Vagrantfile` consist more manual setup for demonstating how to do same things by editing config files.

####################################

However, in my opinion, I need to know how to setup network configuration in another distributives.
For this reason - another setup in file `vagrant-int-net-nat/Vagrantfile`


```
3: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:7e:d3:1e brd ff:ff:ff:ff:ff:ff
    inet 172.16.17.1/24 brd 172.16.17.255 scope global enp0s9
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe7e:d31e/64 scope link
       valid_lft forever preferred_lft forever
4: enp0s10: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:38:d2:d7 brd ff:ff:ff:ff:ff:ff
    inet 10.5.92.2/24 metric 100 brd 10.5.92.255 scope global dynamic enp0s10
       valid_lft 459sec preferred_lft 459sec
    inet6 fe80::a00:27ff:fe38:d2d7/64 scope link
       valid_lft forever preferred_lft forever
vagrant@client1:~$ ping 10.5.92.1
PING 10.5.92.1 (10.5.92.1) 56(84) bytes of data.
64 bytes from 10.5.92.1: icmp_seq=1 ttl=64 time=1.30 ms
64 bytes from 10.5.92.1: icmp_seq=2 ttl=64 time=0.573 ms
^C
--- 10.5.92.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1027ms
rtt min/avg/max/mdev = 0.573/0.934/1.296/0.361 ms
vagrant@client1:~$ tracepath -n 10.5.92.1
 1?: [LOCALHOST]                      pmtu 1500
 1:  10.5.92.1                                             0.915ms reached
 1:  10.5.92.1                                             0.714ms reached
     Resume: pmtu 1500 hops 1 back 1
vagrant@client1:~$ route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         _gateway        0.0.0.0         UG    100    0        0 enp0s3
default         _gateway        0.0.0.0         UG    100    0        0 enp0s10
10.0.2.0        0.0.0.0         255.255.255.0   U     100    0        0 enp0s3
_gateway        0.0.0.0         255.255.255.255 UH    100    0        0 enp0s3
_gateway        0.0.0.0         255.255.255.255 UH    100    0        0 enp0s10
10.0.2.3        0.0.0.0         255.255.255.255 UH    100    0        0 enp0s3
10.5.92.0       0.0.0.0         255.255.255.0   U     100    0        0 enp0s10
172.16.17.0      0.0.0.0         255.255.255.0   U     0      0        0 enp0s9
resolver1.opend _gateway        255.255.255.255 UGH   100    0        0 enp0s10
```

# Below result of partially automated vagrant setup 
## in dir bridge-intnet-lo_routes

## task 4-5
```
# route: cl2 -> server -> cl1

cl1:~$ sudo ip addr add 172.17.37.1/24 dev lo
cl1:~$ sudo ip addr add 172.17.27.1/24 dev lo
cl1:~$ ip a sh lo
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet 172.17.37.1/24 scope global lo
       valid_lft forever preferred_lft forever
    inet 172.17.27.1/24 scope global lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever


server:~$ sudo ip route add 172.17.27.1 dev eth3
server:~$ ping 172.17.27.1
PING 172.17.27.1 (172.17.27.1): 56 data bytes
64 bytes from 172.17.27.1: seq=0 ttl=42 time=3.821 ms
^C
--- 172.17.27.1 ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 3.821/3.821/3.821 ms

# let's check route: cl2 -> server -> cl1
cl2:~$ ping 172.17.27.1
PING 172.17.27.1 (172.17.27.1): 56 data bytes
64 bytes from 172.17.27.1: seq=0 ttl=42 time=3.276 ms
^C
--- 172.17.27.1 ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 3.276/3.276/3.276 ms
cl2:~$ traceroute 172.17.27.1
traceroute to 172.17.27.1 (172.17.27.1), 30 hops max, 46 byte packets
 1  one.one.one.one (1.1.1.1)  0.070 ms  0.978 ms  0.585 ms
 2  172.17.27.1 (172.17.27.1)  3.365 ms  1.063 ms  0.611 ms

# directly route: cl2 -> cl1 by intnet4

cl2:~$ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.1.19    0.0.0.0         UG    0      0        0 eth1
1.1.1.0         0.0.0.0         255.255.255.0   U     0      0        0 eth1
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
172.16.17.0      0.0.0.0         255.255.255.0   U     0      0        0 eth2
172.17.27.1     192.168.1.19    255.255.255.255 UGH   0      0        0 eth1
192.168.1.0     192.168.1.19    255.255.255.0   UG    0      0        0 eth1
192.168.1.19    0.0.0.0         255.255.255.255 UH    0      0        0 eth1
cl2:~$ sudo ip addr add 172.17.37.1/24 dev lo
cl2:~$ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.1.19    0.0.0.0         UG    0      0        0 eth1
1.1.1.0         0.0.0.0         255.255.255.0   U     0      0        0 eth1
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
172.16.17.0      0.0.0.0         255.255.255.0   U     0      0        0 eth2
172.17.27.1     192.168.1.19    255.255.255.255 UGH   0      0        0 eth1
192.168.1.0     192.168.1.19    255.255.255.0   UG    0      0        0 eth1
192.168.1.19    0.0.0.0         255.255.255.255 UH    0      0        0 eth1
cl2:~$ ping 172.17.37.1
PING 172.17.37.1 (172.17.37.1): 56 data bytes
64 bytes from 172.17.37.1: seq=0 ttl=42 time=0.281 ms
^C
--- 172.17.37.1 ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 0.281/0.281/0.281 ms
cl2:~$ traceroute 172.17.37.1
traceroute to 172.17.37.1 (172.17.37.1), 30 hops max, 46 byte packets
 1  172.17.37.1 (172.17.37.1)  0.060 ms  0.082 ms  0.074 ms
cl2:~$

# delete previous routes
cl2:~$ sudo ip route del 172.17.27.1 via 192.168.1.19
server:~$ sudo ip route del 172.17.27.1 dev eth3
cl2:~$ ping 172.17.27.1
PING 172.17.27.1 (172.17.27.1): 56 data bytes
^C
--- 172.17.27.1 ping statistics ---
96 packets transmitted, 0 packets received, 100% packet loss

# 000 | 01101 & 000 | 10111 <= 11100000 mask <= 224 <= /19 cidr
# 172.17.0.0/19
# that need for additional networks areas, like 172.17.|28|.xxx etc

server:~$ sudo ip route add 172.17.0.0/19 via 1.1.1.1
server:~$ ping 172.17.27.1
PING 172.17.27.1 (172.17.27.1): 56 data bytes
64 bytes from 172.17.27.1: seq=0 ttl=42 time=5.529 ms
^C
--- 172.17.27.1 ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 5.529/5.529/5.529 ms

# test with new cidr 172.17.0.0/19
# cl2 -> server (192.168.1.1 - 1.1.1.1 - 2.2.2.1) -> 172.17.27.1 (cl1 lo iface)

cl2:~$ ping 172.17.27.1
PING 172.17.27.1 (172.17.27.1): 56 data bytes
64 bytes from 172.17.27.1: seq=0 ttl=42 time=5.733 ms
64 bytes from 172.17.27.1: seq=1 ttl=42 time=1.580 ms
^C
--- 172.17.27.1 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 1.580/3.656/5.733 ms
cl2:~$ traceroute 172.17.27.1
traceroute to 172.17.27.1 (172.17.27.1), 30 hops max, 46 byte packets
 1  2.2.2.1 (2.2.2.1)  1.750 ms  1.211 ms  1.296 ms
 2  172.17.27.1 (172.17.27.1)  0.060 ms  1.498 ms  0.848 ms
cl2:~$ traceroute 172.17.37.1
traceroute to 172.17.37.1 (172.17.37.1), 30 hops max, 46 byte packets
 1  2.2.2.1 (2.2.2.1)  0.065 ms  0.060 ms  0.055 ms
 2  172.17.37.1 (172.17.37.1)  0.912 ms  0.840 ms  0.370 ms
cl2:~$
```

## task 6
``` cl1:~$ ssh-keygen -t rsa -f /home/vagrant/.ssh/id_rsa_cl1 -q -P "" ```
-P is the passphrase option, "" is the empty passphrase
-q quiet (without verbose output)

# change to no password auth.
```
server:~$ cat /etc/ssh/sshd_config 
AuthorizedKeysFile	.ssh/authorized_keys

Subsystem	sftp	/usr/lib/ssh/sftp-server
# change to yes in yout want to stay with password login
PasswordAuthentication no
PermitRootLogin yes

UseDNS no
```

```
# after that changes in conf file need to restart sshd
server:~$ sudo rc-service sshd restart
# now try 
cl1:~$ ssh -i ~/.ssh/id_rsa_cl1 vagrant@192.168.1.19
server:~$
``

server:~$ sudo iptables -A INPUT -i eth3 -p tcp --dport 22 -j DROP
server:~$ sudo iptables -nvL --line-numbers
Chain INPUT (policy ACCEPT 25 packets, 1244 bytes)
num   pkts bytes target     prot opt in     out     source               destination         
1        0     0 DROP       tcp  --  eth3   *       0.0.0.0/0            0.0.0.0/0            tcp dpt:22

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
num   pkts bytes target     prot opt in     out     source               destination         

Chain OUTPUT (policy ACCEPT 17 packets, 1300 bytes)
num   pkts bytes target     prot opt in     out     source               destination         
cl2:~$ time ssh -v vagrant@192.168.1.19
OpenSSH_7.7p1, LibreSSL 2.7.5
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: Connecting to 192.168.1.19 [192.168.1.19] port 22.
^C

real	0m23.553s
user	0m0.015s
sys	0m0.011s
cl2:~$ ping 192.168.1.19
PING 192.168.1.19 (192.168.1.19): 56 data bytes
64 bytes from 192.168.1.19: seq=0 ttl=42 time=0.937 ms
^C
--- 192.168.1.19 ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 0.937/0.937/0.937 ms
```
## add rule for block by 172.17.37.1 from Client 2
## with icmp protocol 
```
server:~$ sudo iptables -I INPUT -p icmp -s 172.17.37.1 -j DROP
server:~$ sudo iptables -A INPUT -i eth3 -p tcp --dport 22 -j DROP

server:~$ sudo iptables -nvL --line-numbers
Chain INPUT (policy ACCEPT 67 packets, 3004 bytes)
num   pkts bytes target     prot opt in     out     source               destination         
1        0     0 DROP       icmp --  *      *       172.17.37.1          0.0.0.0/0           
2        0     0 DROP       tcp  --  eth3   *       0.0.0.0/0            0.0.0.0/0            tcp dpt:22

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
num   pkts bytes target     prot opt in     out     source               destination         

Chain OUTPUT (policy ACCEPT 58 packets, 4452 bytes)
num   pkts bytes target     prot opt in     out     source               destination         


cl2:~$ ping 172.17.123.1
PING 172.17.123.1 (172.17.123.1): 56 data bytes
^C
--- 172.17.123.1 ping statistics ---
2 packets transmitted, 0 packets received, 100% packet loss
cl2:~$ ping 172.17.27.1
PING 172.17.27.1 (172.17.27.1): 56 data bytes
64 bytes from 172.17.27.1: seq=0 ttl=42 time=2.574 ms
^C
--- 172.17.27.1 ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 2.574/2.574/2.574 ms
cl2:~$
```