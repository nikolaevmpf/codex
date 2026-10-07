# Ubuntu 24.04: Zabbix 7.0 + PostgreSQL

Пример сервера с Nginx. Для прокси см. [[Proxy]]. Пакеты репозитория проверяйте по [официальной инструкции](https://www.zabbix.com/download).

## Репозиторий и пакеты

```bash
wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.0+ubuntu24.04_all.deb
sudo dpkg -i zabbix-release_latest_7.0+ubuntu24.04_all.deb
sudo apt update
sudo apt install postgresql postgresql-contrib zabbix-server-pgsql \
  zabbix-frontend-php php8.3-pgsql zabbix-nginx-conf zabbix-sql-scripts zabbix-agent
```

## База данных

Пароль роли задайте в запросе программы и сохраните в менеджере паролей.

```bash
sudo -u postgres createuser --pwprompt zabbix
sudo -u postgres createdb -O zabbix zabbix

# Импорт выполняется один раз, в пустую базу
set -o pipefail
zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz \
  | sudo -u zabbix psql -v ON_ERROR_STOP=1 -d zabbix
```

Это подключение через локальный сокет с peer-аутентификацией. Если она не настроена для роли `zabbix`, используйте TCP с паролем роли **вместо** команды импорта выше:

```bash
set -o pipefail
zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz \
  | psql -h localhost -U zabbix -d zabbix -W -v ON_ERROR_STOP=1
```

## Конфигурация

В `/etc/zabbix/zabbix_server.conf` задайте пароль созданной роли:

```ini
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=REPLACE_WITH_DB_PASSWORD
```

Не оставляйте пароль из примера. В `/etc/zabbix/nginx.conf` проверьте:

```nginx
listen 80;
server_name zabbix.example.local;
```

Проверьте конфликты с другими виртуальными хостами. Для доступа вне доверенной сети настройте HTTPS.

```bash
sudo locale-gen ru_RU.UTF-8
sudo nginx -t
sudo systemctl enable --now postgresql zabbix-server zabbix-agent nginx php8.3-fpm
sudo systemctl restart zabbix-server nginx php8.3-fpm
```

## Проверка

```bash
systemctl status zabbix-server nginx php8.3-fpm
sudo tail -n 30 /var/log/zabbix/zabbix_server.log
```

Откройте `http://адрес-сервера/` и завершите веб-мастер. Для конфигурации Nginx выше путь `/zabbix` обычно не нужен.

Начальный пользователь — `Admin`, пароль — `zabbix`. Сразу смените его после входа.

## Агент: только активные проверки

На клиенте в `/etc/zabbix/zabbix_agentd.conf`:

```ini
StartAgents=0
ServerActive=10.2.1.60
HostnameItem=system.hostname
HostMetadataItem=system.uname
```

Замените адрес сервера. Имя узла в Zabbix должно совпадать с именем агента; `HostMetadataItem` нужен только при использовании авторегистрации.

```bash
sudo systemctl enable --now zabbix-agent.service
sudo systemctl restart zabbix-agent.service
```
