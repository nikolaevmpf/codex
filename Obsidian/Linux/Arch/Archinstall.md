# Запись USB

	sudo wipefs --all /dev/sdx
	sudo dd bs=4M if=archlinux.iso of=/dev/sdx status=progress iflag=sync
# Установка
	loadkeys ru
	setfont cyr-sun16 (стандартный шрифт)
	setfont ter-c32b (для мониторов 4К)
	archinstall

# Обновление
	sudo pacman -Syu

# Удаление лишнего
	sudo pacman -Rs epiphany gnome-connections gnome-tour gnome-software simple-scan malcontent gnome-maps htop

# Установка нужного pacman
	sudo pacman -S base-devel git ncdu mc papirus-icon-theme bash-completion ufw  transmission-gtk btop nmap vi cronie rsync obsidian telegram-desktop
	sudo systemctl enable --now ufw.service
	sudo systemctl enable --now cronie.service
	sudo ufw enable
# AUR
	git clone https://aur.archlinux.org/yay.git
	cd yay
	makepkg -sir --needed --noconfirm --skippgpcheck
	cd 
	rm -rf yay/

# Установка нужного aur
	yay -S google-chrome bibata-cursor-theme gnome-shell-extension-dash-to-dock

# Настройка

	gsettings set org.gnome.shell.extensions.dash-to-dock click-action 'minimize'
	gsettings set org.gnome.desktop.interface color-scheme 'prefer-dark'
	gsettings set org.gnome.desktop.interface cursor-theme 'Bibata-Modern-Classic'
	gsettings set org.gnome.desktop.interface icon-theme 'Papirus'
	gsettings set org.gnome.desktop.background picture-uri ''
	gsettings set org.gnome.desktop.background picture-uri-dark ''
	gsettings set org.gnome.desktop.background primary-color '#000000'
	gsettings set org.gnome.desktop.background secondary-color '#000000'
	gsettings set org.gnome.Console custom-font 'Adwaita Mono 14'
	gsettings set org.gnome.Console use-system-font false
	gsettings set org.gnome.desktop.wm.preferences button-layout 'appmenu:minimize,maximize,close'
# ZSH

	sudo pacman -S zsh git curl
	chsh -s $(which zsh)
	sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
	
#####
	sed -i 's/ZSH_THEME="robbyrussell"/ZSH_THEME="arrow"/g' /home/nikolaev/.zshrc
	sudo pacman -S zsh-syntax-highlighting zsh-autocomplete zsh-autosuggestions zsh-history-substring-search zsh-completions
	echo "source /usr/share/zsh/plugins/zsh-autocomplete/zsh-autocomplete.plugin.zsh" >> ${ZDOTDIR:-$HOME}/.zshrc
	echo "source /usr/share/zsh/plugins/zsh-history-substring-search/zsh-history-substring-search.zsh" >> ${ZDOTDIR:-$HOME}/.zshrc
	echo "source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh" >> ${ZDOTDIR:-$HOME}/.zshrc
	echo "source /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ${ZDOTDIR:-$HOME}/.zshrc

	echo "alias upd=’sudo apt update && sudo apt full-upgrade’" >> ~/.zshrc
	echo "alias df='df -h'" >> ~/.zshrc
	source ~/.zshrc