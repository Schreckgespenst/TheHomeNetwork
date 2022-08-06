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

8. Get Portainer
sudo docker run -d -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:linux-arm
then go to the following in browser:
http://OrionRaspi.local:9000
add username and password

9. Get Heimdall
 # list current uid and gid, note these for later
         id $user
         # make a heimdall directory to mount in the container
         mkdir ~/heimdall
         # run the heimdall docker image
         # replace PUID, GUID with the output of the id $user command above
         docker run \
        --name=heimdall \
        -e PUID=1000 \
        -e PGID=1000 \
        -e TZ=Asia/Calcutta \
        -p 8006:80 \
        -v ~/heimdall:/config \
        --restart unless-stopped \
        linuxserver/heimdall
   Open a web browser and navigate to http://orionraspi.local:8006/

10. Get PiHole
create a script file
      sudo nano pihole.sh
paste the contents of pihole.sh into it
      sudo chmod u+x pihole.sh
execute the script
       sudo ./pihole.sh
 add to heimdall, go to http://192.168.1.10/admin/ and login
 
11. Mounting a disk

Create a directory in the /mnt directory:

sudo mkdir /mnt/1

We change the access rights to the directory. Only root and only read and write.

sudo chmod -R 660 /mnt/1

We mount:

sudo mount /dev/sda2 /mnt/1

To mount the disk automatically when the system boots, edit the /etc/fstab file. Open with any text editor, for example, nano:

sudo nano /etc/fstab

At the very end of the file, insert the line:

/dev/sda2 /mnt/1 ext4 defaults 0 0

Save the file.

12. Setting up samba
We can install the packages that we require to setup Samba by running the following command.
      sudo apt-get install samba samba-common-bin
      
Now we can share this folder using the Samba software. To do this, we need to modify the samba config file.
The “smb.conf” configuration file is where you will store all your settings for your shares.
We can begin modifying the config file by running the command below.

      sudo nano /etc/samba/smb.conf
      sudo apt install -y samba samba-common-bin smbclient cifs-utils
      
Within this file, add the following to the bottom. This text defines various details of our share.
       [OrionShare]
        path = /dev/sda2 /mnt/1
        writeable=Yes
        create mask=0777
        directory mask=0777
        public=no
Run the following command to create the user. You will be prompted afterward to enter the password.
        sudo smbpasswd -a OrionPi
