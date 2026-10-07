# Переход с Netplan на systemd-networkd

[[Содержание|Главное оглавление]] · [[Linux/Network/Обзор|Сеть Linux]]

Netplan генерирует настройки для networkd или NetworkManager. Удалять `netplan.io` обычно не требуется: можно оставить `renderer: networkd`.

## Если нужен прямой конфиг networkd

> [!warning] Сеть может отключиться
> Делайте переход с локальной консоли и сохраните существующие YAML-файлы и `/etc/resolv.conf`.

1. Узнайте интерфейс: `ip link`.
2. Подготовьте соответствующий `.network` по заметке [[Linux/Network/Systemd-networkd|Сеть: systemd-networkd]].
3. Если переходите полностью, уберите YAML-файлы Netplan из `/etc/netplan/` в резервный каталог.
4. Выполните `sudo netplan generate`, проверьте `/run/systemd/network/`: старые сгенерированные настройки не должны перекрывать новый файл.
5. Только после подготовки отключите прежний менеджер, если он использовался.

```bash
# Только при полном отказе от NetworkManager
sudo systemctl disable --now NetworkManager.service
sudo systemctl enable --now systemd-networkd.service systemd-resolved.service

# После резервного копирования resolv.conf
sudo ln -sfn /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf
sudo systemctl restart systemd-networkd.service
```

## Проверка

```bash
networkctl status
resolvectl status
ip route
getent hosts ubuntu.com
```

Удаление `netplan.io` рассмотрите отдельно, после проверки работы сети и зависимостей пакета.

## Связанные заметки

- [[Linux/Ubuntu/Install|Ubuntu: базовая настройка]]
- [[Linux/Astra/Network|Astra Linux: статический IP]]
