# Arch Linux: очистка

## Кэш пакетов

```bash
sudo pacman -S --needed pacman-contrib
sudo paccache -r  # Оставить три последние версии пакетов
yay -Sc          # Просмотреть запрос перед подтверждением
```

## Неиспользуемые зависимости

```bash
# Сначала просмотреть список
pacman -Qtdq

# Удалить только если список не пуст; выполнить в Bash
mapfile -t orphans < <(pacman -Qtdq)
if ((${#orphans[@]})); then
  sudo pacman -Rns "${orphans[@]}"
fi
```

## Журналы и временные файлы

```bash
journalctl --disk-usage
sudo journalctl --vacuum-time=14d
sudo systemd-tmpfiles --clean
```

Не обнуляйте `/var/log/pacman.log` и не удаляйте весь `/tmp`: они нужны для диагностики и работающих программ.

## Кэш браузера и шрифтов

Закройте браузер; его кэш лучше очищать через настройки, сохранив пароли и историю.

```bash
fc-cache -f  # Перестроить кэш шрифтов текущего пользователя
```
