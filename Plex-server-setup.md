# Home plex ubuntu setup with T400 passthrough

## **Plex install**
### install all needed stuff for plex
```sh
echo deb https://downloads.plex.tv/repo/deb public main | sudo tee /etc/apt/sources.list.d/plexmediaserver.list && curl https://downloads.plex.tv/plex-keys/PlexSign.key | sudo apt-key add - && sudo apt update -y && sudo apt install plexmediaserver -y && sudo apt upgrade =y
```
---
## **Zerotier install**
### ZT install with join command
```sh
curl -s https://install.zerotier.com | sudo bash && sudo zerotier-cli join 8bd5124fd6367c53
```
---
## **Move plex from port 32400 to port 80**
### **add** rc.local
```bash
sudo nano /etc/rc.local
```
### content **/etc/rc.local**
```sh
#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.

# plex setup
IP=$(/sbin/ip -o -4 addr list ens18 | awk '{print $4}' | cut -d/ -f1)
PORT=32400

iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination $IP:$PORT

exit 0
```

### **Prerun setup**
```bash
sudo chmod +x /etc/rc.local
```
---
## **T400 passthrough**
### **Create downloads folder and download nvidia drivers**
```sh
mkdir /home/kopy/downloads && cd /home/kopy/downloads && wget https://us.download.nvidia.com/XFree86/Linux-x86_64/515.76/NVIDIA-Linux-x86_64-515.76.run
```
### **Blacklist default drivers**
```sh
sudo bash -c "echo blacklist nouveau > /etc/modprobe.d/blacklist-nvidia-nouveau.conf" && sudo bash -c "echo options nouveau modset=0 >> /etc/modprobe.d/blacklist-nvidia-nouveau.conf" && sudo update-initramfs -u 
```
### **Make .run file exec and install deps**
```sh
sudo chmod +x NVIDIA-Linux-x86_64-515.76.run && sudo apt install build-essential libglvnd-dev pkg-config -y
```

### **REBOOT**
```sh
sudo reboot now
```
### **install drivers**
```sh
sudo /home/kopy/downloads/NVIDIA-Linux-x86_64-515.76.run
```
### **REBOOT**
```sh
sudo reboot now
```