### NETWORKING

192.182.1.101/24 -- > CIDR Notation ( Classless Inter-Domain Routing )
              |
              |
             24 means First 24 bits of this address are the prefix of this network. 

Binary Notation of the above IP addresss is 

    192        182       1        101
    11000000.10110110.00000001.01100101
    {   First 24 bits        } { last 8 bits  }
    {     Network Prefid     } { Specific Device on the network}


192.182.1.255 is part of same network.
192.182.2.100 is a different network.


IPV4 -  32 Bits        8/8/8/8 ( Decimal )
IPV6 -  128 Bits       16/16/16/16/16/16/16/16   ( Hexa Decimal )



CONFIGURING IPV4 and IPV6 NETWORKING AND HOSTNAME RESOLUTION
=============================================================
```bash
ip link
This shows our networking devices

ip addr
ip address
ip a
This command gives us the IP Details

ip -c addr/a/address

ip link set dev <dev-name> up/down 
This command changes the status of the Network/link device to Up/Down

ip addr add CIDR dev <dev-name>
Eg: ip addr add 10.0.2.16/24 dev enp0s3
This command sets the given IP address to the link device

NOTE: A network interface/dev can have multiple IPs

To remove a IP,
ip addr delete CIDR dev <dev-name>
Eg: ip addr delete 10.0.2.16/24 dev enp0s3


---------------------------
NOTE: The problem with setting IP or changing settings using IP command are temporary.
As soon as your system reboots, the changes will be gone.
---------------------------

NETPLAN is the tool we can use to make our changes permanant.

Netplan will send the instructions to systemd's Network daemon called systemd-networkd.

we can configs under /etc/netplan/<some-name>.yaml

NOTE: Trick to find the sample file -> /usr/share/doc/netplan/examples/static.yaml

network:
    ethernets:
        enp0s3:
            dhcp4: false
            dhcp6: false
            addresses:
                - 10.2.0.1/24
            nameservers:
                addresses:
                    - 8.8.4.4
                    - 8.8.8.8
            routes:
                - to: 192.168.0.0/24
                  via: 10.0.0.100
                - to: default
                  via: 10.0.0.1
    version: 2


> netplan try
> netplan apply

To check the network routes:
> ip route

To add default gateway:
> ip route add default via <IP> dev <Interface name>

If you have 2 interfaces with different default gateways:
> ip route del default via <IP> dev <Interface name>

To add nameservers at global level:
We need to uncomment DNS under /etc/systemd/resolved.conf

To apply changes,  
> sudo systemtctl restart systemd-resolved.service

To check the DNS,
> resolvectl status

To check only the dns server details
> resolvectl dns


==================================  NOTE =======================================
You can find example configuration files at /usr/share/doc/netplan/examples/


```
Bridge: Glues two or more networks and make it look lika a single network

/etc/netplan/mybridge.yaml
---------------------------
network:
    version: 2
    renderer: networkd
    ethernets:
        enp0s8:
            dhcp4: no
        enp0s9:
            dhcp4: no
    bridges:
        br0:
            dhcp4: yet
            interfaces:
                - enp0s8
                - enp0s9

Bond: Glue two or more connections to a network and represent them as a single connection

Bonding Modes:
-------------

Bonding mode can be defined using number 0 to 6

Mode 0 - RoundRobin
Mode 1 - Active-backup
Mode 2 - XOR
Mode 3 - broadcast
Mode 4 - IEEE 802.3ad  ( more through put )
Mode 5 - adaptive transmit load balancing
Mode 6 - adaptive load balancing

/etc/netplan/bond.yaml
------------------------
network:
    version: 2
    renderer: networkd
    ethernets:
        enp0s8:
            dhcp4: no
        enp0s9:
            dhcp4: no
    bonds:
        bond0:
            dhcp4: yes
            interfaces:
                - enp0s8
                - enp0s9
            parameters:
                mode: active-backup
                primary: enp0s8ip 

sudo ufw allow 22

sudo ufw enable

sudo ufw route allow from 10.0.0.0/24 to 192.168.0.5

man ufw-framework

sudo iptables --list-rules --table nat

sudo iptables --flush --table nat

NOTE: IPTABLE rules cant survive accross restarts.  Following below 3 commands helps to save and load the iptables rules across restarts

sudo apt install iptables-presistent  (this binary helps us to save the iptable rules)
sudo netfilter-persistent save ( Command to save the iptable rules)
sudo systemctl restart netfilter-persistent.service


iptables \
    --table nat \  # Work on the nat table
    --append PREROUTING \  # Append the rule to the PREROUTING chain
    --protocol tcp \ # Match packets with tcp protocol
    --dport 80 \ # Match packets going to port 80
    --jump REDIRECT \ # If rule matched, jump to redirect target
    --to 8080 # change destination port to 80


To DROP connections from a IP
Syntax:
sudo iptables \
    -I INPUT \
    -s 192.168.1.7 \
    -j DROP

TO DROP connections if the request comes to a port 80
Syntax:
sudo iptables \
    -I INPUT \
    --proto tcp \
    --dport 80 \
    -j DROP

TO ACCEPT conncetions if the request comes to port 80 from a specific IP
Syntax:
sudo iptables \
    -I INPUT \
    --proto tcp \
    --source 192.168.1.7 \
    --dport 80
    -j ACCEPT


Port Forwarding:
-----------------

sudo iptables \
    -t nat \
    -I PREROUTING \
    --proto tcp \
    --dport 8080 \
    --source 192.168.1.0/24 \
    -j DNAT
    --to-destination 192.168.1.8:80


To List rules with numbers:
syntax:
sudo iptables -L --line-numbers

To list rules with numbers and port numbers
Syntax:
sudo iptables -L --line-numbers -n

To Delete the rules with number:
syntax: 
sudo iptables -D INPUT <rule-number>


============================================================= REVERSE PROXY =====================================================================


sudo apt install nginx



==================================================================================================================================================

TIME SERVERS : NETWORK TIME PROTOCOL SERVERS ( NTP SERVERS )

Time related operations can be performed with a "timedatectl" utility


If "timedatectl" reports "NPT Service: inactive" we can install

sudo apt install systemd-timesyncd
sudo timedatectl set-ntp true
sudo systemctl status systemd-timesycnd.service

sudo vim /etc/systemd/timesycnd.conf

========================================================== CONFIGURE SSH SERVERS AND CLIENTS =====================================================

Config file of sshd daemon
----------------------------
sudo vim /etc/ssh/sshd_config

Config file for ssh client
----------------------------
sudo vim /etc/ssh/ssh_config


To enable password less authentication from windows Host to Linux VM:
---------------------------------------------------------------------
Edit /etc/ssh/sshd_config -> 'PasswordAuthentication' set to 'no' and 'KbdInteractiveAuthentication' set to 'no'
Save the changes...

sudo systemctl restart sshd.service
sudo systemctl status sshd.service

ssh-keygen -t rsa -b 4096 -C "user-in-linux@linuxhost-ip"

ssh-copy-id -i ../.ssh/id_rsa.pub vboxuser@192.168.1.8
    or
Manually copy the contents of id_rsa.pub to authorized_keys file



vim .ssh/config
---------------
Host <hostname>
    HostName <IPaddress>
    Port 22
    User <Username>

chmod 600 .ssh/config

To login : ssh hostname


Global settings
----------------
sudo vim /etc/ssh/ssh_config

If we update this file, there is a high chance that we might loose our changes during the system upgrades.

So it is recommended to create new file under /etc/ssh/ss_config.d/99-our-settings.conf




------ If you want to login with ssh instead of password --------

> ssh-keygen ( to generate ssh keys in local computer )
> ssh-copy-id username@IP
        or
> copy the contents of .pub file to ~/.ssh/authorized_keys file
> change the permission to 600


To remove the finger prints of a specific IP address from a known_hosts file,
> ssh-keygen -R IP-address

To remove all finger prints, remove ~/.ssh/known_hosts file
