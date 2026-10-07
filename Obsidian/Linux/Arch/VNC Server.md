# TigerVNC на Arch Linux

Отдельный удалённый рабочий стол X11. Сессия GNOME Wayland для этого примера не подходит.

## Пользователь

```bash
sudo pacman -S --needed tigervnc
vncpasswd
ls /usr/share/xsessions  # Найти доступную X11-сессию
mkdir -p ~/.config/tigervnc
nano ~/.config/tigervnc/config
```

Пример конфигурации — если установлен Openbox:

```ini
session=openbox
geometry=1280x720
dpi=96
localhost
```

`session` — имя файла из `/usr/share/xsessions` без `.desktop`. В старых версиях TigerVNC настройки лежали в `~/.vnc`; проверьте `man vncsession` для своей версии.

## Служба

В `/etc/tigervnc/vncserver.users` добавьте имя своего пользователя:

```ini
:4=username
```

```bash
sudo systemctl enable --now vncserver@:4.service
systemctl status vncserver@:4.service
```

Дисплей `:4` соответствует порту `5904`.

## Подключение через SSH

На клиенте:

```bash
ssh -N -L 5904:localhost:5904 username@server
```

VNC-клиент подключайте к `localhost:5904`. Параметр `localhost` не открывает VNC в сеть; на сервере должен работать [[ssh|SSH]].
