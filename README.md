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

# Scripts for the Wi-Fi Hotspot

Put the Scripts in your root folder: ./pi/
To execute the scripts you first need to add the execution permission to both files.
In the terminal write: 

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