# Universidad de Zaragoza Wi-Fi HotSpot
Activation and Desactivation BASH Scripts for Wi-Fi Hotspot on Raspberry Pi B+

## Configure a Static IP on Ethernet (Optional)

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

## Scripts for the Wi-Fi Hotspot

Place the files in your root directory: /home/pi/

Before to execute the scripts you first need to add the execution permission to both files.  
To give scripts full access to the Owner ***u***, the Owner-Group ***g*** and Everyone else ***o***, write in the terminal: 

~~~~
chmod g+rwx DesactiveZarHotSpot
chmod u+rwx DesactiveZarHotSpot 
chmod o+rwx DesactiveZarHotSpot 

chmod g+rwx ActiveZarHotSpot
chmod u+rwx ActiveZarHotSpot 
chmod o+rwx ActiveZarHotSpot 
~~~~

To execute them:

~~~~
./ActiveZarHotSpot
./DesactiveZarHotSpot
~~~~

## One Time Step for First Configuration
Install ***hostapd*** and ***dnsmasq***. Open the Terminal and write:
~~~~
sudo apt-get -y install hostapd dnsmasq
~~~~
Continue in the Terminal:
~~~~
sudo mv /etc/network/interfaces /etc/network/interfaces.bak
sudo nano /etc/network/interfaces
~~~~

At the bottom of that file, add the following:

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
And then, when asked with which name to save the file, write: ***/etc/network/interfaces.hotspot***  
Continue in the Terminal:
~~~~
sudo mv /etc/hostapd/hostapd.conf /etc/hostapd/hostapd.conf.bak
sudo nano /etc/hostapd/hostapd.conf
~~~~
Enter the following into that file. Feel fee to change the ***ssid*** (WiFi network name) and the ***wpa_passphrase*** (password to join the network) to whatever you’d like. You can also change the channel to something in the 1-11 range (if channel 6 is too crowded in your area).

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
And then, when asked with which name to save the file, write:  ***/etc/hostapd/hostapd.conf.hotspot***  
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
