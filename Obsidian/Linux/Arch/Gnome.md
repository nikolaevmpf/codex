pacman -S xorg xorg-server gdm gnome 
systemctl enable gdm


	sudo pacman -Syu
	sudo pacman -S gnome gnome-extra gnome-shell-extensions gnome-tweaks networkmanager pipewire pipewire-pulse pipewire-alsa pipewire-jack
	sudo systemctl enable gdm.service
	sudo systemctl enable NetworkManager.service
	sudo reboot