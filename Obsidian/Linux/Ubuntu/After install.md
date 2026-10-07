# Ubuntu 24.04: после установки

[[Содержание|Главное оглавление]] · [[Linux/Ubuntu/Обзор|Ubuntu]]

Выбирайте нужные программы; устанавливать всё сразу необязательно. Сеть и драйверы: [[Linux/Ubuntu/Install|Базовая настройка]].

## Оглавление заметки

- [[Linux/Ubuntu/After install#Обновление|Обновление]]
- [[Linux/Ubuntu/After install#Программы из APT и Snap|Программы из APT и Snap]]
- [[Linux/Ubuntu/After install#Chrome — пакет DEB|Chrome — пакет DEB]]
- [[Linux/Ubuntu/After install#Flatpak — альтернативный источник|Flatpak — альтернативный источник]]
- [[Linux/Ubuntu/After install#GNOME|GNOME]]
- [[Linux/Ubuntu/After install#Snap — удаление только при необходимости|Snap — удаление только при необходимости]]
- [[Linux/Ubuntu/After install#VMware Workstation|VMware Workstation]]
- [[Linux/Ubuntu/After install#VPN|VPN]]

## Обновление

```bash
sudo apt update
sudo apt full-upgrade
sudo apt autoremove  # Проверить список перед подтверждением
```

## Программы из APT и Snap

```bash
sudo apt install gnome-tweaks timeshift ncdu inxi nmap htop mc tcpdump \
  gnome-browser-connector gnome-shell-extensions dconf-editor \
  kdenlive shotcut mpv heif-gdk-pixbuf
sudo snap install telegram-desktop
sudo snap install obsidian --classic
```

Steam доступен также через Flatpak. Для `heif-gdk-pixbuf` может потребоваться репозиторий universe.

## Chrome — пакет DEB

Для x86_64; альтернатива — Flatpak ниже.

```bash
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt install ./google-chrome-stable_current_amd64.deb
rm google-chrome-stable_current_amd64.deb
```

`apt install ./...` устанавливает зависимости; `--force-depends` не нужен.

## Flatpak — альтернативный источник

```bash
sudo apt install flatpak gnome-software-plugin-flatpak
sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

Выберите приложения из списка:

```bash
flatpak install flathub ca.desrt.dconf-editor org.telegram.desktop \
  com.valvesoftware.Steam com.transmissionbt.Transmission org.remmina.Remmina \
  org.gnome.Boxes com.google.Chrome io.mpv.Mpv org.kde.kdenlive org.shotcut.Shotcut
```

GNOME Boxes подходит для простых ВМ; для расширенных настроек используйте virt-manager. Не ставьте одновременно несколько вариантов одной программы без необходимости.

## GNOME

Расширения: Dash to Dock, Gradient Top Bar, Hide Top Bar, User Themes, ddterm. Проверьте совместимость с версией GNOME.

```bash
# Только если доступна схема Ubuntu Dock / Dash to Dock
gsettings set org.gnome.shell.extensions.dash-to-dock click-action 'minimize'
```

В `~/.bashrc` добавьте один раз:

```bash
alias upd='sudo apt update && sudo apt full-upgrade && sudo snap refresh'
```

## Snap — удаление только при необходимости

```bash
snap list
# Пример удаления ненужных приложений
sudo snap remove firefox thunderbird
```

Удаление всего Snap несовместимо с установкой Telegram и Obsidian через Snap выше. Сначала перенесите данные и выберите альтернативы; каталог `~/snap` автоматически не удаляйте.

## VMware Workstation

Скачайте актуальный `.bundle` с официального портала Broadcom. Не используйте старую ссылку на Workstation 16.

```bash
# Заменить имя файла скачанным
chmod +x VMware-Workstation-VERSION.x86_64.bundle
sudo ./VMware-Workstation-VERSION.x86_64.bundle
```

## VPN

```bash
sudo apt install network-manager-openvpn-gnome network-manager-openconnect-gnome
```

OpenConnect подходит для совместимых серверов Cisco AnyConnect. Профиль создайте в настройках сети.

## Связанные заметки

- [[Linux/Ubuntu/Cloud-init|Ubuntu: отключение cloud-init]]
