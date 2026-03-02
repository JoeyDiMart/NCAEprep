# NCAE
- top of this file will have minimum configuration set up (give the machine an IP)
- second section will be specific configs for each team (how to set up web server, router, etc.)
- third section will be command line tools for different VMs (like adding additional IPs, etc.)
- fourth section has file paths for config files in order by machine 
- last section is random notes for ncae






=============================================================================================================================================================## Kali Network Interface minimum Configuration
## Kali machine Network Interface minimum Configuration
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
- how to set up a static IP or dhcp for CentOS machine
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


## Ubuntu machine Network Interface Minimal Configuration
- this Ubuntu machine will be for hosting the internal web server, these instructions will only give a static IP
- it's possible to have more than one .yaml file with network configurations, but should only be one
1. check the directory "/etc/netplan"
2. the file you want to edit is "01-network-manager-all.yaml" full command to run to edit this:
```shell
sudo nano /etc/netplan/01-network-manager-all.yaml
```
3. After editing to get an IP address, it should look like this:
```shell
# Let NetworkManager manage all devices on this system
network:
  version: 2
  renderer: NetworkManager
  ethernets: 
    ens18:
      addresses: {IP_they_told_you_to_put}/{netmask}
```
- Keep in mind of the spacing of this file everything under network for example is "two space bars" of space not a tab
- if there are other interfaces, put it at the same tab as ens18 under "ethernets"
- addresses = IP address (can be multiple) and the netmask is probably /24 
4. save nano file "control + X -> y -> enter"
5. restart the service to apply changes to netplan with:
```shell
sudo netplan apply
```
- test with "ip a"





=============================================================================================================================================================
# Configure each machine for the CTF 

##





=============================================================================================================================================================
# Command Line steps/processes for different machines

## Ubuntu/Kali/CentOS Machine using CLI to add a temporary IP address (not added to config file)
- will not survive restarting even though it will add the IP address
```shell
sudo ip addr app {IP_address_you_want_to_add}/{netmask} {where_to_assign} {what_service}
```
- where to assign will probably be "dev" for what device  
- what service will probably be "ens18" for the network adapter


## Ubuntu/Kali/CentOS Machine trashing all IP addresses (IP flushing)
- temp IP addresses can be added maliciously or accidentally, this command can help to restore this and delete all the temp ones
```shell
sudo ip addr flush dev ens18
sudo netplan apply  # for Ubuntu machine only
sudo systemctl restart networking  # for Kali machine only
sudo systemctl retart network  # for CentOS machine only
```





=============================================================================================================================================================
# Kali conf files
## /etc/network/interfaces
- will give network config 


## CentOS conf files
#### /etc/sysconfig/network-scripts/
- in this directory you'll see a list of files like "ifcfg-eth0" for network configuration





=============================================================================================================================================================
## Notes/points for NCAE
- Router with basic configuration (just an internal + external IP) means only devices outside of the network can reach/ping the external IP
and only devices within the network can reach the internal IP, router needs the gateway/port forwarding set up so for example traffic looking for port 80
connects to the webserver (inside the network)
- "ifconfig" won't show you additional temp IP addresses that you added to your network device (use "ip a")







