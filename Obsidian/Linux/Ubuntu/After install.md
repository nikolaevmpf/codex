UBUNTU 24.04

# Обновление

	sudo apt update && sudo apt -y dist-upgrade && sudo apt -y autoremove 

# Удаление ненужных программ

	sudo snap remove firefox thunderbird

# Установка Chrome

	wget https://dl.google.com/linux/direct/google-chrome-table_current_amd64.deb && sudo dpkg -i --force-depends google-chrome-stable_current_amd64.deb && sudo rm google-chrome-stable_current_amd64.deb

# Установка программ

	sudo apt install -y gnome-tweaks timeshift ncdu inxi neofetch nmap htop mc tcpdump chrome-gnome-shell gnome-shell-extensions dconf-editor steam
	sudo snap install telegram-desktop
	sudo snap install obsidian --classic

# поддержка формата .heic

	sudo apt -y install heif-gdk-pixbuf

# Добавим alias

	sudo echo "alias upd='sudo apt update && sudo apt -y dist-upgrade && sudo apt autoremove && sudo snap refresh'" >> ~/.bashrc

# Минимизация окна по иконки

	gsettings set org.gnome.shell.extensions.dash-to-dock click-action 'minimize'

	sudo shutdown -r now








Dash to Dock
Gradient top bar
Hide top bar
User themes
ddTerm

sudo systemctl stop snapd
sudo apt remove --purge --assume-yes snapd
sudo rm -rf ~/snap/

Установка программ
sudo apt install -y flatpak
sudo apt install -y gnome-software-plugin-flatpak
sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

sudo flatpak install -y flathub ca.desrt.dconf-editor 
sudo flatpak install -y flathub org.telegram.desktop
sudo flatpak install -y flathub com.valvesoftware.Steam
sudo flatpak install -y flathub com.transmissionbt.Transmission
sudo flatpak install -y flathub org.remmina.Remmina
sudo flatpak install -y flathub org.gnome.Boxes # Нет снимков и проброса USB
sudo flatpak install -y flathub com.google.Chrome
sudo flatpak install -y flathub io.mpv.Mpv
sudo flatpak install -y flathub org.kde.kdenlive
sudo flatpak install -y flathub org.shotcut.Shotcut


Установка VMware Workstation
wget https://download3.vmware.com/software/WKST-1623-LX-New/VMware-Workstation-Full-16.2.3-19376536.x86_64.bundle
sudo chmod +x VMware-Workstation-.bundle
sudo ./VMware-Workstation-.bundle
sudo echo "mks.gl.allowBlacklistedDrivers = "TRUE"" >> ~/.vmware/preferences


Установка VPN anyconnect
sudo apt install -y network-manager-openvpn network-manager-openconnect-gnome 
