# Light & secure ETH2 validator on Raspberry Pi

# Prerequisites

Get & install Ubuntu server 20 LTS from https://www.raspberrypi.com on your Raspberry Pi4 
SSD USB 3 plugged

# Configure your Raspberry Pi for ETH2

#Displacing /home from SD card to the SSD USB 3 
sudo fdisk -l
sudo mount /dev/sda1 /mnt
sudo cp -rp /home/* /mnt
sudo mv /home /home.orig
sudo mkdir /home
cd /
sudo umount /dev/sda1
sudo mount /dev/sda1 /home/
nano /etc/fstab
to add the folling entry: /dev/sda1  /home  ext4  defaults  0  0

Checking
df /dev/sda1

#Adding a swap file
cd /home ;sudo fallocate -l 16G ./swapfile
sudo chmod 600 swapfile
sudo mkswap swapfile
sudo swapon swapfile
nano /etc/fstab
to add the folling entry: /swapfile swap swap defaults 0 0

# Optional configuration
Deactivating wifi if not used :
sudo apt install net-tools
sudo ifconfig wlan0 down
Securing your installation
Deactivate login for ssh and add your public key in home/debian/.ssh/authorized_keys
install and configure fail2ban
install and configure ufw:
sudo ufw allow 22
sudo ufw allow 30303
sudo ufw allow 12000/udp
sudo ufw allow 13000/tcp

# Initialize key
copy your  eth2deposit-cli validator_keys in ~/eth2.0-deposit-cli/validator_keys
mkdir /home/ubuntu/prysm && cd prysm
curl https://raw.githubusercontent.com/prysmaticlabs/prysm/master/prysm.sh --output prysm.sh && chmod +x prysm.sh
./prysm.sh validator accounts import –keys-dir=$HOME/eth2.0-deposit-cli/validator_keys
then remove directory : ~/eth2.0-deposit-cli/validator_keys

# ETH1 node
To avoid creating an ETH1 node, we will use infura API
Create your account and get your key (it’s free)

# Last step
To allow your ssh commands to keep running when disconnected
sudo apt install tmux

# Running your ETH2 validator node
Synchonize time with command:
timedatectl

Open 2 command terminal with tmux :

first one run ETH1:
./prysm.sh beacon-chain --http-web3provider=https://mainnet.infura.io/v3/YourApiKey

second one run ETH2 validator:
./prysm.sh validator --graffiti="#FreeAssange"

you can now detach tmux terminals (ctrl+b d), exit your ssh connection and monitor your node on https://beaconcha.in/validator/YourValidatorID
