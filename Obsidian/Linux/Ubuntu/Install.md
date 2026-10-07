# Ubuntu: базовая настройка

Примеры для Ubuntu 24.04. Адреса, интерфейсы и домен замените своими. Приложения: [[After install]], прокси: [[Proxy]], мониторинг: [[Zabbix]].

## Сеть через Netplan

Сначала проверьте существующие файлы в `/etc/netplan/`, чтобы не задать интерфейс дважды. В GNOME можно использовать NetworkManager и `nmtui`.

Пример для Ubuntu Server, `/etc/netplan/02-network.yaml`:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    ens160:
      dhcp4: false
      addresses: [10.2.1.150/24]
      nameservers:
        addresses: [10.2.1.11, 10.2.1.218]
        search: [gz.local]
      routes:
        - to: default
          via: 10.2.1.1
```

```bash
sudo chmod 600 /etc/netplan/02-network.yaml
sudo netplan generate
sudo netplan try  # Временное применение с подтверждением
```

Изменение сети может оборвать SSH; держите доступ к консоли.

## Обновление и SSH

```bash
sudo apt update
sudo apt full-upgrade
sudo apt install openssh-server
sudo systemctl enable --now ssh.service
```

Настройка ключей: [[ssh]]. Перед включением UFW разрешите порт SSH: [[ufw]].

В `~/.bashrc` можно добавить один раз:

```bash
alias upd='sudo apt update && sudo apt full-upgrade'
```

## Видеодрайверы

```bash
ubuntu-drivers devices
# NVIDIA: установить рекомендованный драйвер
sudo ubuntu-drivers install
```

Для AMD обычно достаточно штатного ядра и Mesa. Старый пакет `fglrx-driver` не используйте.

## Звук

```bash
systemctl --user status pipewire pipewire-pulse wireplumber
```

Ubuntu 24.04 Desktop использует PipeWire. Правку `tsched=0` в `/etc/pulse/default.pa` рассматривайте только для старых систем с PulseAudio, после диагностики.

## Гостевая VMware

```bash
sudo apt install open-vm-tools
# Для графического рабочего стола
sudo apt install open-vm-tools-desktop
```

## Полезные команды

```bash
ip address       # Сетевые адреса
lsblk -f         # Диски и файловые системы
sudo blkid       # UUID разделов
sudo apt install ncdu inxi cdpr
ncdu /           # Занятое место; без sudo некоторые каталоги недоступны
inxi -F          # Оборудование
sudo cdpr -d eth1 # Обнаружение CDP; заменить интерфейс
```

```bash
sudo reboot      # Перезагрузка
# sudo poweroff  # Выключение
```
