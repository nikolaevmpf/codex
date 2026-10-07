# UFW: межсетевой экран

[[Содержание|Главное оглавление]] · [[Linux/Utility/Обзор|Утилиты и сервисы Linux]]

## Установка

Arch Linux:

```bash
sudo pacman -Syu ufw
```

Ubuntu / Debian:

```bash
sudo apt install ufw
```

## Базовые правила

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing

# До включения UFW, если нужен SSH на стандартном порту
sudo ufw limit 22/tcp
sudo ufw enable
```

При нестандартном порте SSH замените `22`. Для удалённой настройки сначала разрешите доступ, иначе соединение может оборваться.

На Arch включите службу:

```bash
sudo systemctl enable --now ufw.service
```

## Дополнительные правила

```bash
# SSH только из своей подсети — альтернатива открытому правилу выше
sudo ufw allow from 192.168.0.0/24 to any port 22 proto tcp

# Профили приложений: использовать только существующие
sudo ufw app list
# sudo ufw allow Deluge
# sudo ufw delete allow Deluge
```

Не разрешайте всю подсеть ко всем портам без необходимости. Правила суммируются: для ограничения SSH подсетью удалите прежнее общее правило.

## Проверка и удаление

```bash
sudo ufw status verbose
sudo ufw status numbered
# Удалить выбранное правило по актуальному номеру
sudo ufw delete 1
```

После удаления номера правил меняются.

## Связанные заметки

- [[Linux/Ubuntu/Zabbix|Ubuntu 24.04: Zabbix 7.0 + PostgreSQL]]
- [[Linux/Ubuntu/Proxy|Ubuntu: прокси]]
- [[Linux/Utility/Vaultwarden|Vaultwarden: Docker + Nginx]]
- [[Linux/Utility/rsync|rsync: резервное копирование]]
- [[Linux/Utility/ssh|SSH: ключи и сервер]]
