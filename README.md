# Wi-Fi HotSpot for Universidad de Zaragoza with Raspberry Pi 3 B+
Activation and Desactivation BASH Scripts for Wi-Fi Hotspot on Raspberry Pi 3 B+  
Before using the scripts is important to follow the One-Time procedure to setup the network.

## To execute the Scripts for the Wi-Fi Hotspot, write in the Terminal:

~~~~
./ActiveZarHotSpot
./DesactiveZarHotSpot
~~~~

## One Time Step for First Configuration
It's necessary to install ***hostapd*** and ***dnsmasq***. Open the Terminal and write:
~~~~
sudo apt-get -y install hostapd dnsmasq
~~~~
Continue in the Terminal:
~~~~
sudo mv /etc/network/interfaces /etc/network/interfaces.bak
sudo nano /etc/network/interfaces
~~~~

At the bottom of that file, add the following lines:

~~~~
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp

allow-hotplug wlan0
iface wlan0 inet static
    address 192.168.5.1
    netmask 255.255.255.0
    network 192.168.5.0
    broadcast 192.168.5.255
~~~~

***Control+x*** to Exit  
***Y*** to Save  
and then, when asked with which name to save the file, write: ***/etc/network/interfaces.hotspot***  

Continue in the Terminal:
~~~~
sudo mv /etc/hostapd/hostapd.conf /etc/hostapd/hostapd.conf.bak
sudo nano /etc/hostapd/hostapd.conf
~~~~
Enter the following lines into the ***hostapd.conf*** file. Feel fee to change the ***ssid*** (WiFi network name) and the ***wpa_passphrase*** (password to join the network) to whatever you’d like. You can also change the channel to something in the 1-11 range (if channel 6 is too crowded in your area).

~~~~
interface=wlan0
driver=nl80211
ssid=MyPiAP
hw_mode=g
channel=6
ieee80211n=1
wmm_enabled=1
ht_capab=[HT40][SHORT-GI-20][DSSS_CCK-40]
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_key_mgmt=WPA-PSK
wpa_passphrase=raspberry
rsn_pairwise=CCMP
~~~~

***Control+x*** to Exit  
***Y*** to Save  
and then, when asked with which name to save the file, write:  ***/etc/hostapd/hostapd.conf.hotspot***  
  
Continue in the Terminal:
~~~~
sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.bak
sudo nano /etc/dnsmasq.conf
~~~~

In the blank file, paste in the text below. Note that we set up DHCP to assign addresses to devices between 192.168.5.100 and 192.168.5.200. Remember that 192.168.5.1 is reserved for the Pi. So, anything between 192.168.5.2 - 192.168.5.9 and between 192.168.5.201 - 192.168.5.254 can be used for devices with static IP addresses.

~~~~
interface=wlan0 
listen-address=192.168.5.1
bind-interfaces 
server=8.8.8.8
domain-needed
bogus-priv
dhcp-range=192.168.5.100,192.168.5.200,24h
~~~~
Continue in the Terminal:
~~~~
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE  
sudo iptables -A FORWARD -i eth0 -o wlan0 -m state --state RELATED,ESTABLISHED -j ACCEPT
sudo iptables -A FORWARD -i wlan0 -o eth0 -j ACCEPT
sudo sh -c "iptables-save > /etc/iptables.ipv4.nat"
~~~~

Reference Guide: https://learn.sparkfun.com/tutorials/setting-up-a-raspberry-pi-3-as-an-access-point/all

#### How to prepare the Scripts to be executed for the Wi-Fi Hotspot

Now it's time to place the Scripts files ***ActiveZarHotSpot*** and ***DesactiveZarHotSpot*** in your root directory: ***/home/pi/***  

Before to execute the scripts you first need to add the execution permission to both files.  
To give to the Owner ***u***, the Owner-Group ***g*** and Everyone else ***o***, full access to the Scripts, write in the terminal: 

~~~~
chmod g+rwx DesactiveZarHotSpot
chmod u+rwx DesactiveZarHotSpot 
chmod o+rwx DesactiveZarHotSpot 

chmod g+rwx ActiveZarHotSpot
chmod u+rwx ActiveZarHotSpot 
chmod o+rwx ActiveZarHotSpot 
~~~~

## Configure a Static IP on Ethernet (Optional)
If your Network uses Static IPs you want your Raspberry Pi to use one of them. To do that, follow this lines below.  
Open the Raspberry Pi Terminal and write:

~~~~
sudo nano /etc/dhcpcd.conf
~~~~

Add these lines at the end of the file:

~~~~
interface eth0 
static ip_address = XX.XX.XX.XX
static routers = XX.XX.XX.XX
static domain_name_servers = XX.XX.XX.XX
~~~~

- **ip_address** is the IP STATIC address
- **routers** is the address of your router (or gateway)
- **domain_name_servers** is the DNS address(es)  
  
To get those values: ***ipconfig*** on Windows Terminal or ***ifconfig*** in Non-Windows machines Terminal.
