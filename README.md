# Universidad de Zaragoza Wi-Fi HotSpot
Activation and Desactivation BASH Scripts for Wi-Fi Hotspot on Raspberry Pi B+

# Configure a Static IP on Ethernet

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

