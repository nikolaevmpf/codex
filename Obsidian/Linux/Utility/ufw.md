sudo pacman -S ufw

sudo ufw default deny
sudo ufw allow from 192.168.0.0/24
sudo ufw allow Deluge
sudo ufw limit ssh

sudo ufw enable

sudo ufw status

sudo ufw delete allow Deluge