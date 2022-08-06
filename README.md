# TheHomeNetwork

1. Put the image on a sd card
2. configure ssh
3. ssh into your raspi
4. Check for updates
sudo apt-get update
sudo apt-get upgrade

5. Get Network Manager
sudo apt-get install network-manager
5.1 Turn off random mac generation
  To disable the MAC address randomization create the file

sudo nano /etc/NetworkManager/conf.d/100-disable-wifi-mac-randomization.conf
with the content:

[connection]
wifi.mac-address-randomization=1
 
[device]
wifi.scan-rand-mac-address=no

ip/mac address can be checked by
ip r | grep default
or
ifconfig

6. set a static ip by dhcp binding
(can also be done by raspi by editing the file:
sudo nano /etc/dhcpcd.conf
and adding this at the end:
interface eth0
static ip_address=192.168.1.10
static routers=192.168.1.1
static domain_name_servers=192.168.1.1 
)
7. Install Docker
curl -fsSL get.docker.com -o get-docker.sh && sh get-docker.sh
(this command alone may not work for raspi 4; check compatibility)
Add user to docker group
  sudo usermod -aG docker ${USER}
  su - ${USER}
