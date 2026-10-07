# Wi-Fi: подключение

[[Содержание|Главное оглавление]] · [[Linux/Arch/Обзор|Arch Linux]]

## Проверка блокировки

```bash
rfkill list
sudo rfkill unblock wifi
```

`SOFT blocked` снимается командой. При `HARD blocked` проверьте аппаратный переключатель или UEFI.

## В live-образе Arch Linux: iwctl

```bash
iwctl
```

Внутри `iwctl` замените `wlan0` своим интерфейсом из `device list`:

```text
device list
station wlan0 scan
station wlan0 get-networks
station wlan0 connect "Имя сети"
exit
```

Пароль вводится в запросе программы. `iwd` должен быть запущен; само подключение требует также настройки IP и DNS.

## В GNOME: NetworkManager

Не запускайте независимые сетевые менеджеры для одного интерфейса.

```bash
nmcli device status
nmcli device wifi list
nmcli --ask device wifi connect "Имя сети"
```

## Проверка

```bash
ip address
ip route
ping -c 3 archlinux.org
```

## Связанные заметки

- [[Linux/Arch/Archinstall|Arch Linux: установка через archinstall]]
- [[Linux/Arch/Gnome|Arch Linux: GNOME]]
