# TFTP на Arch Linux

TFTP не использует аутентификацию и шифрование. Пример — для выдачи файлов в доверенной локальной сети.

## Установка

```bash
sudo pacman -S --needed tftpd-hpa
sudo install -d -m 755 /srv/tftp
sudo nano /etc/conf.d/tftpd
```

Параметры сервера:

```ini
TFTPD_ARGS="--secure -4 /srv/tftp"
TFTPD_USER="nobody"
TFTPD_GROUP="nobody"
```

`--secure` ограничивает каталог, `-4` включает только IPv4. Проверьте, что группа `nobody` существует: `getent group nobody`.

## Файлы и запуск

```bash
# Заменить путь к файлу
sudo install -m 644 /path/to/file /srv/tftp/
sudo systemctl enable --now tftpd.service
systemctl status tftpd.service
```

Права `777` не нужны для скачивания. Загрузку файлов на сервер включайте отдельно и только при необходимости.

Для UFW разрешите UDP 69 только своей подсети; пример:

```bash
sudo ufw allow from 192.168.1.0/24 to any port 69 proto udp
```

Проверка с клиента:

```text
tftp server-address
tftp> get file
tftp> quit
```
