# Vaultwarden: Docker + Nginx

Пример для Arch Linux в локальной сети: `02vault.gz.local`, IP `10.2.1.151`. Замените домен и адрес своими. TLS завершает Nginx; контейнер доступен только на loopback.

## Пакеты

```bash
sudo pacman -Syu
sudo pacman -S --needed docker docker-compose nginx openssl
sudo systemctl enable --now docker.service
sudo install -d -m 700 /opt/vaultwarden
cd /opt/vaultwarden
```

Команды Docker ниже выполняются через sudo. Группа `docker` даёт права, сопоставимые с root; добавление в неё необязательно.

## Прокси Docker — если требуется

Файл `/etc/systemd/system/docker.service.d/proxy.conf`:

```ini
[Service]
Environment="HTTP_PROXY=http://10.2.1.252:8080/"
Environment="HTTPS_PROXY=http://10.2.1.252:8080/"
Environment="NO_PROXY=localhost,127.0.0.1,.gz.local"
```

```bash
sudo mkdir -p /etc/systemd/system/docker.service.d
# После создания файла
sudo systemctl daemon-reload
sudo systemctl restart docker.service
```

## Compose

Создайте `/opt/vaultwarden/compose.yaml`. Замените `VERSION` проверенной версией образа; не обновляйте хранилище паролей вслепую.

```yaml
services:
  vaultwarden:
    image: vaultwarden/server:VERSION
    container_name: vaultwarden
    restart: unless-stopped
    environment:
      DOMAIN: "https://02vault.gz.local"
      SIGNUPS_ALLOWED: "false"
      LOG_LEVEL: "warn"
    volumes:
      - ./data:/data
    ports:
      - "127.0.0.1:8000:80"
```

Современный Vaultwarden обслуживает WebSocket на основном порту; отдельный `3012` и `WEBSOCKET_ENABLED` не нужны. `ADMIN_TOKEN` не задан: административная панель отключена.

```bash
cd /opt/vaultwarden
sudo docker compose config --quiet
sudo docker compose up -d
```

## TLS для локальной сети

Самоподписанный сертификат нужно доверенно установить на клиентах; для публичного домена используйте доверенный CA.

```bash
sudo install -d -m 700 /etc/ssl/vaultwarden
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/ssl/vaultwarden/server.key \
  -out /etc/ssl/vaultwarden/server.crt \
  -subj '/CN=02vault.gz.local' \
  -addext 'subjectAltName=DNS:02vault.gz.local,IP:10.2.1.151'
sudo chmod 600 /etc/ssl/vaultwarden/server.key
```

## Nginx

Создайте `/etc/nginx/conf.d/vaultwarden.conf`. Убедитесь, что в секции `http` файла `/etc/nginx/nginx.conf` есть `include /etc/nginx/conf.d/*.conf;`.

```nginx
server {
    listen 80;
    server_name 02vault.gz.local;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name 02vault.gz.local;
    ssl_certificate /etc/ssl/vaultwarden/server.crt;
    ssl_certificate_key /etc/ssl/vaultwarden/server.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    client_max_body_size 128M;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

```bash
sudo nginx -t
sudo systemctl enable --now nginx.service
sudo systemctl reload nginx.service
```

На клиентах настройте DNS или запись в hosts:

```text
10.2.1.151 02vault.gz.local
```

Разрешите TCP 80/443 из нужной сети в используемом межсетевом экране. Пример UFW:

```bash
sudo ufw allow from 10.2.1.0/24 to any port 80 proto tcp
sudo ufw allow from 10.2.1.0/24 to any port 443 proto tcp
```

## Первый пользователь

Регистрация отключена. Для первого аккаунта временно установите `SIGNUPS_ALLOWED: "true"`, примените Compose и создайте аккаунт в доверенной сети. Сразу верните `"false"` и снова примените:

```bash
cd /opt/vaultwarden
sudo docker compose up -d
```

## Проверка

```bash
sudo docker compose ps
sudo docker compose logs --tail=50 vaultwarden
curl -I http://127.0.0.1:8000/
```

Откройте `https://02vault.gz.local` с доверенным сертификатом, проверьте вход и сохранение тестовой записи.

## Резервная копия и обновление

Сначала сохраните каталог `data`, Compose и TLS-ключи в защищённое хранилище. Для согласованной копии остановите контейнер на время копирования:

```bash
cd /opt/vaultwarden
sudo docker compose stop
# Выполнить резервное копирование data и конфигурации
sudo docker compose start
```

После резервной копии измените версию образа в `compose.yaml`:

```bash
sudo docker compose pull
sudo docker compose up -d
sudo docker compose logs --tail=50 vaultwarden
```

Автоматическое обновление без проверки и резервной копии не настраивайте.

[Документация Vaultwarden](https://github.com/dani-garcia/vaultwarden/wiki)
