<h1> Light & secure ETH2 validator on Raspberry Pi </h1>

<h2> Prerequisites </h2>

<p>Get & install Ubuntu server 20 LTS from https://www.raspberrypi.com on your Raspberry Pi4 <br/>
SSD USB 3 plugged</p>

<h2> Configure your Raspberry Pi for ETH2 </h2>

<h3> Displacing /home from SD card to the SSD USB 3 </h3>
<p>sudo fdisk -l <br/>
sudo mount /dev/sda1 /mnt <br/>
sudo cp -rp /home/* /mnt <br/>
sudo mv /home /home.orig <br/>
sudo mkdir /home <br/>
cd / <br/>
sudo umount /dev/sda1 <br/>
sudo mount /dev/sda1 /home/ <br/>
</p>
<p>
nano /etc/fstab <br/>
to add the following entry: /dev/sda1  /home  ext4  defaults  0  0
</p>

Checking <br/>
df /dev/sda1 <br/>

<h3> Adding a swap file </h3>

cd /home ;sudo fallocate -l 16G ./swapfile <br/>
sudo chmod 600 swapfile <br/>
sudo mkswap swapfile <br/>
sudo swapon swapfile <br/>
nano /etc/fstab <br/>
to add the following entry: /swapfile swap swap defaults 0 0 <br/>

<h2> Optional configuration </h2>
Deactivating wifi if not used : <br/>
sudo apt install net-tools <br/>
sudo ifconfig wlan0 down <br/>

<h2> Securing your installation </h2> 
Deactivate login for ssh and add your public key in home/debian/.ssh/authorized_keys <br/>
install and configure fail2ban <br/>
install and configure ufw: <br/>
sudo ufw allow 22 <br/>
sudo ufw allow 30303 <br/>
sudo ufw allow 12000/udp <br/>
sudo ufw allow 13000/tcp <br/>

<h2> Initialize key </h2> 
copy your  eth2deposit-cli validator_keys in ~/eth2.0-deposit-cli/validator_keys <br/>
mkdir /home/ubuntu/prysm && cd prysm <br/>
curl https://raw.githubusercontent.com/prysmaticlabs/prysm/master/prysm.sh --output prysm.sh && chmod +x prysm.sh <br/>
./prysm.sh validator accounts import –keys-dir=$HOME/eth2.0-deposit-cli/validator_keys <br/>
then remove directory : ~/eth2.0-deposit-cli/validator_keys <br/>

<h2> ETH1 node </h2> 
To avoid creating an ETH1 node, we will use infura API <br/>
Create your account and get your key (it’s free) <br/>

<h2>  Last step </h2> 
To allow your ssh commands to keep running when disconnected : <br/>
sudo apt install tmux <br/>

<h2>  Running your ETH2 validator node </h2> 
Synchonize time with command: <br/>
timedatectl <br/>

<p>Open 2 command terminal with tmux : </p>

first one run ETH1: <br/>
./prysm.sh beacon-chain --http-web3provider=https://mainnet.infura.io/v3/YourApiKey <br/>

second one run ETH2 validator: <br/>
./prysm.sh validator --graffiti="#FreeAssange" <br/>

you can now detach tmux terminals (ctrl+b d), exit your ssh connection and monitor your node on https://beaconcha.in/validator/YourValidatorID
