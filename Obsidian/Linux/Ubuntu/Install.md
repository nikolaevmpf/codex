### NETWORK
nmtui

sudo nano /etc/netplan/02-network.yaml

	network:
	  ethernets:
	    ens160:
	      addresses:
	      - 10.2.1.150/24
	      nameservers:
	        addresses:
	        - 10.2.1.11
	        - 10.2.1.218
	        search:
	        - gz.local
	      routes:
	      - to: default
	        via: 10.2.1.1
	  version: 2

PROXY SYSTEM

sudo nano  /etc/profile.d/proxy.sh

	export http_proxy="http://10.2.1.252:8080/"
	export https_proxy="http://10.2.1.252:8080/"
	export ftp_proxy="http://10.2.1.252:8080/"
	export no_proxy="127.0.0.1,localhost"
	export HTTP_PROXY="http://10.2.1.252:8080/"
	export HTTPS_PROXY="http://10.2.1.252:8080/"
	export FTP_PROXY="http://10.2.1.252:8080/"
	export NO_PROXY="127.0.0.1,localhost"

sudo chmod +x  /etc/profile.d/proxy.sh
source /etc/profile.d/proxy.sh
env | grep -i proxy

### APT

sudo nano /etc/apt/apt.conf.d/80proxy

	Acquire::http::proxy "http://10.2.1.252:8080/";
	Acquire::https::proxy "http://10.2.1.252:8080/";
	Acquire::ftp::proxy "ftp://10.2.1.252:8080/";

### WGET

sudo nano /etc/wgetrc

	use_proxy = on
	http_proxy = http://10.2.1.252:8080 
	https_proxy = http://10.2.1.252:8080
	ftp_proxy = http://10.2.1.252:8080

### SSH Server

sudo apt install openssh-server
sudo systemctl enable sshd

### UPDATE

sudo apt update 
sudo apt upgrade
sudo apt autoremove
sudo apt update && sudo apt upgrade -y

Добавление alias:
echo "alias upd=’sudo apt update && sudo apt full-upgrade’" >> ~/.bashrc

### REBOOT

sudo systemctl reboot
sudo systemctl poweroff
sudo systemctl restart networking

### VIDEO

NVIDIA

sudo apt install nvidia-driver

AMD

sudo apt install fglrx-driver

### AUDIO

Артефакты звука
sudo nano /etc/pulse/default.pa

Изменить строку
load-module module-udev-detect

на 

load-module module-udev-detect tsched=0

Перезапуск

pulseaudio -k && pulseaudio --start

### VMWARE

sudo apt install open-vm-tools

COMMAND

ip a - сетевые настройки
blkid - просмотр файловых систем
lsblk - просмотр дисков

### INSTALL

sudo apt install kdenlive - видеоредактор
sudo apt install shotcut - видеоредактор
sudo apt install mpv - видеопроигрыватель
sudo apt install dconf-editor - редактор настроек
sudo apt install gnome-tweak-tool
sudo apt install timeshift

wget [https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb](https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb)
sudo dpkg -i --force-depends google-chrome-stable_current_amd64.deb

sudo apt install cdpr - обнаружение устройств ( использование протокола cdp)
cdpr -d eth1

sudo apt-get install ncdu - подсчет занимаемого места

ncdu /

sudo apt install inxi - информация об оборудовании 

inxi -Fxs

Расширения

Dash to Dock
Gradient top bar
Hide top bar
User themes

Zabbix agent

sudo apt update
sudo apt -y install zabbix-agent

sudo sed -i 's/Server=127.0.0.1/# Server=127.0.0.1/g' /etc/zabbix/zabbix_agentd.conf && sudo sed -i 's/# StartAgents=3/StartAgents=0/g' /etc/zabbix/zabbix_agentd.conf && sudo sed -i 's/ServerActive=127.0.0.1/ServerActive=10.2.1.60/g' /etc/zabbix/zabbix_agentd.conf && sudo sed -i 's/# HostnameItem=system.hostname/HostnameItem=system.hostname/g' /etc/zabbix/zabbix_agentd.conf && sudo sed -i 's/# HostMetadataItem=/HostMetadataItem=system.uname/g' /etc/zabbix/zabbix_agentd.conf

sudo service zabbix-agent restart
sudo service zabbix-agent status

sudo nano /etc/zabbix/zabbix_agentd.conf

Server=127.0.0.1
StartAgents=0
ServerActive=10.2.1.60
HostnameItem=system.hostname
HostMetadataItem=system.uname
