# Astra Linux: статический IP

Замените интерфейс, адрес, шлюз и DNS своими. Выберите способ для используемого сетевого менеджера.

> [!warning] Удалённое подключение
> Изменение сети может оборвать SSH. Желателен доступ к локальной консоли.

## NetworkManager

```bash
nmcli connection show
sudo nmcli connection modify "Имя подключения" \
  ipv4.method manual ipv4.addresses "192.168.1.100/24" \
  ipv4.gateway "192.168.1.1" ipv4.dns "192.168.1.1"
sudo nmcli connection up "Имя подключения"
```

Вернуть DHCP:

```bash
sudo nmcli connection modify "Имя подключения" ipv4.method auto \
  ipv4.addresses "" ipv4.gateway "" ipv4.dns ""
sudo nmcli connection up "Имя подключения"
```

## ifupdown

Если интерфейс управляется `/etc/network/interfaces`, отредактируйте его существующий блок:

```bash
sudo nano /etc/network/interfaces
```

```text
auto eth0
iface eth0 inet static
    address 192.168.1.100
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 192.168.1.1
```

`dns-nameservers` требует интеграции с resolver, например `resolvconf`. Не добавляйте второй блок для того же интерфейса.

```bash
sudo systemctl restart networking.service
```

## Проверка

```bash
ip address
ip route
getent hosts astralinux.ru
```

Если система использует Netplan, настройка выполняется в `/etc/netplan/*.yaml`; см. [[Linux/Ubuntu/Install|Ubuntu: сеть]].
