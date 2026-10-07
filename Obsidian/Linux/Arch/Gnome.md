# Arch Linux: GNOME

[[Содержание|Главное оглавление]] · [[Linux/Arch/Обзор|Arch Linux]]

## Установка

```bash
sudo pacman -Syu
sudo pacman -S --needed gnome gnome-tweaks gnome-shell-extensions \
  networkmanager pipewire pipewire-pulse pipewire-alsa wireplumber
sudo systemctl enable gdm.service
sudo systemctl enable NetworkManager.service
```

Не включайте второй дисплейный менеджер или сетевой менеджер для того же интерфейса. `gnome-extra` — необязательный набор дополнительных приложений.

## Оформление

Выполняйте в своей сессии GNOME, без sudo. Темы предварительно установите; см. [[Linux/Arch/Archinstall|Arch Linux: установка через archinstall]].

```bash
# Тёмный стиль, значки, курсор
gsettings set org.gnome.desktop.interface color-scheme 'prefer-dark'
gsettings set org.gnome.desktop.interface icon-theme 'Papirus'
gsettings set org.gnome.desktop.interface cursor-theme 'Bibata-Modern-Classic'

# Чёрный фон и кнопки окна справа
gsettings set org.gnome.desktop.background picture-uri ''
gsettings set org.gnome.desktop.background picture-uri-dark ''
gsettings set org.gnome.desktop.background primary-color '#000000'
gsettings set org.gnome.desktop.background secondary-color '#000000'
gsettings set org.gnome.desktop.wm.preferences button-layout ':minimize,maximize,close'
```

## Dash to Dock

После установки расширения выйдите и войдите в сеанс. Проверьте идентификатор:

```bash
gnome-extensions list
gnome-extensions enable dash-to-dock@micxgx.gmail.com
gsettings set org.gnome.shell.extensions.dash-to-dock click-action 'minimize'
```

Если схема не найдена, проверьте установку и совместимость расширения с версией GNOME.

## GNOME Console

Только для установленного `gnome-console`; выберите доступный шрифт.

```bash
gsettings set org.gnome.Console use-system-font false
gsettings set org.gnome.Console custom-font 'Adwaita Mono 14'
```

## Проверка

```bash
systemctl status gdm NetworkManager
systemctl --failed
# Перезагрузка — после сохранения работы
sudo reboot
```

## Связанные заметки

- [[Linux/Arch/Install|Arch Linux: ручная установка UEFI]]
