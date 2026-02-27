# NCAE

## Kali Network Interface minimum Configuration
- external Kali machine most likely for the red teamers
- How to set a static IP or dhcp for a Kali VM 
1. Go to “/etc/network/” the file is called “interfaces” (or just sudo nano /etc/network/interfaces)
2. Configure this file to set eth0 to be the following:
```shell
auto eth0
iface eth0 inet static 
  address {put_in_IP_they_told_you_to_put}
  netmask {find_this_from_the_number_after_"/"_they_gave}
```
- auto eth0 = automatically make this network interface start automatically if you restart your computer 
- iface eth0 inet static/dhcp = iface {name_of_interface} {inet=Internet_Address_v4} static/dhcp either set your own IP or get this from dhcp server 
  - if second line is set to dhcp, don't include the address or netmask line
- address {IP} = the IP address you want this network interface to get
- netmask {range} = defines what portion of IP is for the network and what portion is for devices
  - make sure that address and netmask are 1 tab over (looks different bc this is a .md file)
  - netmask help = /24 → 255.255.255.0, /16 → 255.255.0.0
3. this file now has the minimum configuration for a static IP address, other information like "gateway" and "dns" server can be added:
4. To make this service apply your changes, run this:
```shell
sudo systemctl restart networking
```
5. test/check your new IP by running:
```shell 
ip a
```


## CentOS machine Network Interface minimum Configuration
- how to set up a stati cIP or dhcp for CentOS machine
- CentOS machine is most likely for the Router since it's easy to set up both internal and external IP and can communicate across both networks
1. go to the "/etc/sysconfig/network-scripts/" directory
2. this network service has a file for each network interface, example "ifcfg-eth0", "ifcfg-lo" for eth0 and loopback
- Kali has one file for all interfaces fyi
- for the ncae competition it might matter if eth0 is for the external IP and another interfcace for the internal IP, this example will put External as eth0 and Internal as eth1
3. go into the ifcfg-eth0 file with this commmand:
```shell
sudo vi ifcfg-eth0  # full path is /etc/sysconfig/network-scripts/ifcfg-eth0
```
4. There are already things in here already, after your changes it should look like this:
```shell
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=eth0
UUID={some_long_uuid_in_hex}
DEVICE=eth0
ONBOOT=yes
IPADDR={IP_they_told_you_to_put}
NETMASK={put_netmask_given_here}
```
- BOOTPROTO=static means set IP addr to static, but can be set to dhcp and you can remove "IPADDR" and "NETMASK"
- ONBOOT=yes means to automatically start this service after restarting the device (default it's at no)
- to save in vi press "esc -> :wq -> enter" to write and quit changes (without sudo changes won't work)
5. service needs to be restarted for changes to take affect by running:
```shell
sudo systemctl restart network
```
- check if the restart worked with "ip a"

