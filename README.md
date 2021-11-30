# Wireguard VPN
 
## Preinstallation 
This project requires a little bit of setup from: 
    - DigitalOcean 
    - Droplet 
    - Docker
### DigitalOcean 
   1. Create a digital ocean account [here](https://m.do.co/c/4d7f4ff9cfe4). 
        - ***NOTE*** It will prompt for a payment method but will give you a free $100 credit, and you can cancel it before it begins to charge after that $100 limit. (Additionally the account can be deleted and recreated to get that same credit again)
  2. Once created navigate to the API page within the control panel and save this for later 
### Create an Ubuntu 20.04 Droplet 
  1. Within the API page of DigitalOcean select create, then droplet and choose the following settings 
        - Image: Ubuntu 20.04
        - Plan: Basic 
        - CPU: Regular Intel $5/Month 
  2. Select a region for the data center (I choose New York)
  3. Create a password or use SSH (I used a password)
  4. Create Droplet 
  5. Create a token for that new droplet that has read/write access

### Install Docker Within DigitalOcean Console 
   - This can be done with these sets of commands 
     ```
     sudo apt-get update
     sudo apt upgrade
     curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
     echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" |     sudo   tee /etc/apt/sources.list.d/docker.list > /dev/null
     sudo apt update
     sudo apt install docker-ce docker-ce-cli containerd.io
     sudo usermod -aG docker [user]
     sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
     sudo chmod +x /usr/local/bin/docker-compose
     sudo systemctl start docker
     ```
   - ***Note*** [user] should be your name on your Linux machine 
## Install Wireguard On DitialOcean 
1. First issue the commands to make the appropriate directories and yml file 
    ```
    mkdir -p ~/wireguard/
    mkdir -p ~/wireguard/config/
    nano ~/wireguard/docker-compose.yml
    ```
2. Then within the yml file just created copy and paste this information into said file 
     ```
   version: '3.8'
   services:
     wireguard:
       container_name: wireguard
       image: linuxserver/wireguard
       environment:
         - PUID=1000
         - PGID=1000
         - TZ=America/Chicago
         - SERVERURL=68.183.48.11
         - SERVERPORT=51820
         - PEERS=laptop,phone,pc
         - PEERDNS=auto
         - INTERNAL_SUBNET=10.0.0.0
       ports:
         - 51820:51820/udp
       volumes:
         - type: bind
           source: ./config/
           target: /config/
         - type: bind
           source: /lib/modules
           target: /lib/modules
       restart: always
       cap_add:
         - NET_ADMIN
         - SYS_MODULE
       sysctls:
         - net.ipv4.conf.all.src_valid_mark=1
     ```
3. Configure TZ to whatever timezone you're in from the list [here](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)
4. Configure SERVERURL to your DigitalOcean server IP address found on your DigitalOcean control panel 
5. Change peers to however many config files, unique to each device, you want connected to your VPN with unique names 
6. Save the file and exit 
### Start WireGuard 
  - Download the WireGuard app on your device(s) [here](https://www.wireguard.com/install/) on your machine
  - To begin running Wireguard execute to commands within DigitalOcean
    ```
    cd ~/wireguard/
    docker-compose up -d
    ```
  - To find the execution log and or QR code required to connect to the VPN on the WireGuard app execute this command while WireGuard is running 
    ```
    docker-compose logs -f wireguard
    ```
     - Download the congiuration file from the droplet if running on a PC and within WireGuard click add tunnel in the buttom left corner and select your downloaded config file. 
     - ***Reminder***: One config file per device
  - ***Now ativate your VPN on device of your choice and you're done!***
## Completion 
Once completed you now have your VPN up and running. Upon testing you should be able to see location differences from when the VPN is active and when it is off. 
### VPN On Laptop 
![WireGuardLaptop](https://github.com/RyanDerr/Wireguard-VPN/blob/main/Pictures/PCVPN.png)
### VPN on Phone 
#### Before 
![WGPhoneBefore](https://github.com/RyanDerr/Wireguard-VPN/blob/main/Pictures/PhoneBefore.png)
#### After 
![WGPhoneAfter](https://github.com/RyanDerr/Wireguard-VPN/blob/main/Pictures/PhoneAfter.png)
## Completed 
![Completion](https://github.com/RyanDerr/Wireguard-VPN/blob/main/Pictures/Completed.jpg)
