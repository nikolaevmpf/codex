В **Astra Linux** можно отключить **DHCP** и настроить статический IP-адрес через терминал, используя конфигурационные файлы сети или утилиту `nmcli` (если установлен **NetworkManager**).  

### **Способ 1: Через конфигурационный файл (`/etc/network/interfaces`)**
1. **Откройте конфигурационный файл** в текстовом редакторе (например, `nano`):
   ```bash
   sudo nano /etc/network/interfaces
   ```
2. **Найдите интерфейс** (обычно `eth0`, `ens33` или подобный) и **измените** его настройки. Пример для статического IP:
   ```bash
   auto eth0
   iface eth0 inet static
       address 192.168.1.100       # Ваш статический IP
       netmask 255.255.255.0       # Маска подсети
       gateway 192.168.1.1         # Шлюз
       dns-nameservers 8.8.8.8     # DNS-сервер (например, Google)
   ```
   Если DHCP был указан ранее (`iface eth0 inet dhcp`), замените его на `static`.

3. **Перезапустите сеть**:
   ```bash
   sudo systemctl restart networking
   ```
   или
   ```bash
   sudo ifdown eth0 && sudo ifup eth0
   ```

### **Способ 2: Через `nmcli` (если используется NetworkManager)**
1. **Проверьте имя подключения**:
   ```bash
   nmcli con show
   ```
   (обычно это что-то вроде `Wired connection 1` или имя интерфейса, например `eth0`)

2. **Отключите DHCP и задайте статический IP**:
   ```bash
   sudo nmcli con mod "Имя_подключения" ipv4.method manual ipv4.addresses "192.168.1.100/24" ipv4.gateway "192.168.1.1" ipv4.dns "8.8.8.8"
   ```
   (замените `"Имя_подключения"` на актуальное)

3. **Перезапустите подключение**:
   ```bash
   sudo nmcli con down "Имя_подключения" && sudo nmcli con up "Имя_подключения"
   ```

### **Проверка настроек**
Убедитесь, что IP-адрес применился:
```bash
ip a
```
Проверьте маршрут:
```bash
ip route
```
Проверьте DNS:
```bash
cat /etc/resolv.conf
```

Если что-то не работает, проверьте:
- Правильность имени интерфейса (`ip a` или `ifconfig -a`).
- Отсутствие конфликтов IP в сети.
- Корректность шлюза и DNS.

Если используется **netplan** (в новых версиях Astra Linux), нужно править `/etc/netplan/*.yaml`.  

Если нужна помощь с конкретной версией Astra Linux, уточните её (SE, SM, Common Edition).