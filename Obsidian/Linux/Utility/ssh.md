# SSH: ключи и сервер

[[Содержание|Главное оглавление]] · [[Linux/Utility/Обзор|Утилиты и сервисы Linux]]

## Создание ключа на клиенте

```bash
ssh-keygen -t ed25519
```

Задайте парольную фразу. Не перезаписывайте существующий ключ, если он ещё используется.

## Копирование и подключение

```bash
ssh-copy-id username@server
ssh username@server
```

Замените пользователя и адрес. При первом подключении сверяйте отпечаток ключа сервера.

## Сервер Arch Linux

```bash
sudo pacman -Syu openssh
sudo systemctl enable --now sshd.service
```

## Сервер Ubuntu / Debian

```bash
sudo apt update
sudo apt install openssh-server
sudo systemctl enable --now ssh.service
```

## Проверка изменений

```bash
sudo sshd -t  # Проверить конфигурацию до перезапуска
```

Если меняете порт или способ входа, оставьте текущую сессию открытой и проверьте новую. Для UFW см. [[Linux/Utility/ufw|UFW: межсетевой экран]].

## Связанные заметки

- [[Linux/Arch/VNC Server|TigerVNC на Arch Linux]]
- [[Linux/Astra/Ansible|Astra Linux: Ansible]]
