## Удаление netplan 

sudo apt purge netplan.io

sudo systemctl disable network-manager
sudo systemctl stop network-manager
sudo systemctl enable systemd-networkd
sudo systemctl start systemd-networkd
sudo systemctl start systemd-resolved
sudo systemctl enable systemd-resolved
sudo rm /etc/resolv.conf
- sudo ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf

sudo systemctl restart systemd-networkd
sudo systemctl restart systemd-resolved