## Подготовка сервера

1. **Подключение к серверу**
```bash
ssh username@10.2.1.151
```

2. **Настройка временного прокси для текущей сессии**
```bash
export http_proxy=http://10.2.1.252:8080
export https_proxy=http://10.2.1.252:8080
```

3. **Обновление системы**
```bash
sudo pacman -Syu --noconfirm
```

## Установка необходимых пакетов

1. **Установка зависимостей**
```bash
sudo pacman -S --noconfirm docker docker-compose nginx openssl git base-devel
```

2. **Запуск и включение Docker**
```bash
sudo systemctl enable --now docker
```

3. **Добавление пользователя в группу docker**
```bash
sudo usermod -aG docker $USER
newgrp docker
```

## Настройка прокси для Docker

1. **Создание директории конфигурации Docker**
```bash
sudo mkdir -p /etc/systemd/system/docker.service.d
```

2. **Создание файла конфигурации прокси**
```bash
sudo tee /etc/systemd/system/docker.service.d/proxy.conf > /dev/null <<EOL
[Service]
Environment="HTTP_PROXY=http://10.2.1.252:8080"
Environment="HTTPS_PROXY=http://10.2.1.252:8080"
EOL
```

3. **Применение изменений**
```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## Создание самоподписанного SSL-сертификата

1. **Создание директории для сертификатов**
```bash
sudo mkdir -p /etc/ssl/certs/02vault
cd /etc/ssl/certs/02vault
```

2. **Генерация ключа и сертификата**
```bash
sudo openssl req -x509 -nodes -days 3650 -newkey rsa:2048 \
-keyout /etc/ssl/certs/02vault/02vault.key \
-out /etc/ssl/certs/02vault/02vault.crt \
-subj "/CN=02vault.gz.local/O=02vault/C=RU" \
-addext "subjectAltName=DNS:02vault.gz.local,IP:10.2.1.151"
```

3. **Установка прав доступа**
```bash
sudo chmod 600 /etc/ssl/certs/02vault/*
```

## Установка и настройка Vaultwarden

1. **Создание рабочей директории**
```bash
sudo mkdir -p /opt/vaultwarden/{data,config}
cd /opt/vaultwarden
```

2. **Создание docker-compose.yml**
```bash
sudo tee docker-compose.yml > /dev/null <<EOL
version: '3'

services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    restart: always
    environment:
      - WEBSOCKET_ENABLED=true
      - SIGNUPS_ALLOWED=false
      - DOMAIN=https://02vault.gz.local
      - LOG_FILE=/data/vaultwarden.log
      - LOG_LEVEL=warn
      - ADMIN_TOKEN=$(openssl rand -base64 48)
      - ROCKET_TLS={certs="/ssl/02vault.crt",key="/ssl/02vault.key"}
    volumes:
      - ./data:/data
      - /etc/ssl/certs/02vault:/ssl
    ports:
      - "8000:80"
      - "3012:3012"
    networks:
      - vaultwarden_net

networks:
  vaultwarden_net:
    driver: bridge
EOL
```

3. **Запуск Vaultwarden**
```bash
sudo docker-compose up -d
```

## Настройка Nginx в качестве обратного прокси

1. **Создание конфигурации Nginx**
```bash
sudo tee /etc/nginx/conf.d/vaultwarden.conf > /dev/null <<EOL
server {
    listen 80;
    server_name 02vault.gz.local;
    return 301 https://\$host\$request_uri;
}

server {
    listen 443 ssl;
    server_name 02vault.gz.local;

    ssl_certificate /etc/ssl/certs/02vault/02vault.crt;
    ssl_certificate_key /etc/ssl/certs/02vault/02vault.key;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    client_max_body_size 128M;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
    }

    location /notifications/hub {
        proxy_pass http://127.0.0.1:3012;
        proxy_set_header Upgrade \$http_upgrade;
        proxy_set_header Connection "upgrade";
    }

    location /notifications/hub/negotiate {
        proxy_pass http://127.0.0.1:8000;
    }
}
EOL
```

2. **Проверка и перезапуск Nginx**
```bash
sudo nginx -t
sudo systemctl enable --now nginx
sudo systemctl restart nginx
```

## Настройка локального DNS (если нужно)

1. **На клиентских машинах добавить в /etc/hosts**
```
10.2.1.151 02vault.gz.local
```

## Настройка фаервола

1. **Разрешение необходимых портов**
```bash
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
sudo iptables-save | sudo tee /etc/iptables/iptables.rules
sudo systemctl enable --now iptables
```

## Проверка установки

1. **Проверка работы контейнера**
```bash
sudo docker ps
```

2. **Проверка логов**
```bash
sudo docker logs vaultwarden
```

3. **Доступ к веб-интерфейсу**
Откройте в браузере: `https://02vault.gz.local`

4. **Доступ к админ-панели**
`https://02vault.gz.local/admin` с использованием токена из docker-compose.yml

## Дополнительные настройки

1. **Постоянные настройки прокси**
```bash
sudo tee -a /etc/environment > /dev/null <<EOL
http_proxy="http://10.2.1.252:8080"
https_proxy="http://10.2.1.252:8080"
no_proxy="localhost,127.0.0.1,10.2.1.151"
EOL
```

2. **Автоматическое обновление**
Создайте скрипт `/opt/vaultwarden/update.sh`:
```bash
#!/bin/bash
cd /opt/vaultwarden
docker-compose pull
docker-compose up -d
docker image prune -f
```
Сделайте исполняемым:
```bash
sudo chmod +x /opt/vaultwarden/update.sh
```

3. **Добавление в cron для автоматического обновления**
```bash
(crontab -l 2>/dev/null; echo "0 3 * * * /opt/vaultwarden/update.sh >> /var/log/vaultwarden-update.log 2>&1") | crontab -
```

## Важные заметки

1. Для доступа к сервису с других устройств в сети:
   - Добавьте запись `02vault.gz.local` с IP `10.2.1.151` в DNS-сервер сети
   - Или добавьте эту запись в файл hosts на каждом клиентском устройстве

2. При первом посещении браузер будет предупреждать о самоподписанном сертификате - это нормально.

3. Для промышленного использования рекомендуется использовать сертификаты от Let's Encrypt.