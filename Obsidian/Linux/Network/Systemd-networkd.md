# Сеть: systemd-networkd

[[Содержание|Главное оглавление]] · [[Linux/Network/Обзор|Сеть Linux]]

Примеры для интерфейса `enp2s1`; замените имя и адреса своими. Для одного интерфейса используйте один сетевой менеджер.

## Статический адрес

Создайте `/etc/systemd/network/20-wired.network`:

```ini
[Match]
Name=enp2s1

[Network]
Address=10.2.1.111/24
Gateway=10.2.1.1
DNS=10.2.1.11
DNS=10.2.1.218
Domains=gz.local
LinkLocalAddressing=no
IPv6AcceptRA=no
```

Последние две строки отключают link-local и получение IPv6 router advertisements для этого интерфейса, а не весь IPv6 в ядре.

## DHCP — вместо статического блока

Для того же файла:

```ini
[Match]
Name=enp2s1

[Network]
DHCP=yes
```

## DNS и запуск

```bash
sudo systemctl enable --now systemd-networkd.service systemd-resolved.service
# Сначала проверить и сохранить существующий /etc/resolv.conf
sudo ln -sfn /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf
sudo networkctl reload
sudo networkctl reconfigure enp2s1
```

Если нужен глобальный DNS, используйте секцию `[Resolve]` в `/etc/systemd/resolved.conf`; обычно DNS из `.network` достаточно.

## Проверка

```bash
networkctl status enp2s1
resolvectl status
ip route
```

При переходе с другого менеджера см. [[Linux/Network/Netplan|Переход с Netplan на systemd-networkd]]. Применение настроек может оборвать SSH.

## Связанные заметки

- [[Linux/Ubuntu/Install|Ubuntu: базовая настройка]]
- [[Linux/Astra/Network|Astra Linux: статический IP]]
