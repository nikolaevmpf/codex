# NixOS — GNOME и приложения

[[Nix/Обзор|Проект NixOS]] · [[Nix/Применение профиля|Применение конфигурации]]

## Оглавление

- [[Nix/GNOME и приложения#Общий рабочий стол|Общий рабочий стол]]
- [[Nix/GNOME и приложения#Почему настройки не применились|Почему настройки не применились]]
- [[Nix/GNOME и приложения#Ghostty|Ghostty]]
- [[Nix/GNOME и приложения#Программы по профилям|Программы по профилям]]
- [[Nix/GNOME и приложения#MAX и Flatpak|MAX и Flatpak]]
- [[Nix/GNOME и приложения#Amnezia VPN|Amnezia VPN]]
- [[Nix/GNOME и приложения#Виртуальные машины на физических компьютерах|Виртуальные машины на физических компьютерах]]

## Общий рабочий стол

Настройки из modules/gnome.nix:

| Элемент | Значение |
| --- | --- |
| Сеанс | GNOME, GDM; параметры GNOME и графики профиля |
| Браузер | Firefox |
| Терминал | Ghostty, Ctrl+Alt+T |
| Стиль | prefer-dark, Adwaita-dark |
| Фон | Чёрный, без изображения |
| Значки | Papirus-Dark |
| Курсор | Bibata-Modern-Classic |
| Кнопки окон | minimize, maximize, close справа |
| Раскладки | US и RU; обычно Super+пробел |
| Средняя кнопка | Вставка выделенного текста включена |
| Dock | Снизу, autohide и intellihide, полностью прозрачный фон |
| Нажатие на значок | minimize |
| Корзина и диски в Dock | Скрыты |
| Избранное | Firefox, Ghostty, Files; Steam только если включён |

Из GNOME исключены Console, Weather, Calendar, Maps, Contacts, Clocks, Tour, Characters, Connections, Music, Photos, Logs, Remote Desktop, Yelp, Epiphany, Simple Scan, Showtime и Totem. Установка расширения сама по себе не гарантирует его включение у существующего пользователя.

## Почему настройки не применились

Системный dconf задаёт **значения по умолчанию**. Пользовательские значения из старого сеанса имеют приоритет.

Проверять и менять в собственной сессии GNOME, **без sudo**:

~~~bash
gsettings get org.gnome.desktop.interface color-scheme
gsettings get org.gnome.desktop.interface icon-theme
gsettings get org.gnome.shell favorite-apps
gnome-extensions list
~~~

Для возвращения системного значения конкретного ключа:

~~~bash
gsettings reset org.gnome.shell favorite-apps
gsettings reset org.gnome.desktop.interface color-scheme
~~~

Не сбрасывать весь dconf без сохранения личных настроек. После установки расширений выйти и войти в сеанс. Если схема Dash to Dock отсутствует, сначала проверить установку и совместимость расширения.

## Ghostty

Общие настройки: приложение по умолчанию через xdg.terminal-exec; в Files добавлен пункт Open in Ghostty. Физические машины используют собственные ghostty.conf:

| Машина | Исходный файл | Системный файл |
| --- | --- | --- |
| zet | hosts/zet/ghostty.conf | /etc/ghostty/zet.conf |
| nuc | hosts/nuc/ghostty.conf | /etc/ghostty/nuc.conf |
| 02i0132 | hosts/02i0132/ghostty.conf | /etc/ghostty/02i0132.conf |
| vm | Отдельного файла в профиле нет | Общий Ghostty |

Физические профили: JetBrains Mono 12, фон #000000, окно 170 × 45, блочный курсор, без мигания. User-служба ghostty-host-config добавляет config-file в пользовательский конфиг, не заменяя его целиком.

~~~bash
systemctl --user status ghostty-host-config --no-pager
~~~

## Программы по профилям

| Программа / возможность | vm | zet | 02i0132 | nuc |
| --- | --- | --- | --- | --- |
| GNOME, Firefox, Ghostty | Да | Да | Да | Да |
| Steam, GameMode | Нет | Да | Да | Да |
| LibreOffice, Telegram, Obsidian, Pinta, Remmina | Нет | Нет | Да | Да |
| Transmission | Нет | Да | Да | Да |
| Редактор | Не задан отдельно | Не задан отдельно | Zed | VS Code |
| MAX через Flatpak | Нет | Нет | Да | Да |
| Amnezia VPN | Нет | Нет | Да | Да |
| Boxflat / MOZA | Нет | Да | Нет | Нет |
| Хост QEMU/KVM | Нет | Да | Да | Да |
| QEMU Guest Agent | Да | Нет | Нет | Нет |
| Автовход nikolaev | Не объявлен | Да | Не объявлен | Да |

Матрица составлена по коду, а не по устаревшим спискам README.

## MAX и Flatpak

Для 02i0132 и nuc включены Flatpak и install-max.service. Служба добавляет Flathub и устанавливает ru.max.MAX, если его ещё нет. Используется упаковка сообщества. При ошибке повтор через минуту, лимит попытки 15 минут.

~~~bash
systemctl status install-max --no-pager
journalctl -u install-max --no-pager -n 50
flatpak info --system ru.max.MAX
flatpak run ru.max.MAX
~~~

Если нет значка, выйти и войти в GNOME. Обновление отдельно от nix-update:

~~~bash
sudo flatpak update --system ru.max.MAX
~~~

Служба обеспечивает первую установку; уже установленный MAX она не обновляет автоматически.

## Amnezia VPN

В 02i0132 и nuc: programs.amnezia-vpn.enable=true. Открыть приложение и импортировать собственную конфигурацию. Секреты подключения не помещать в заметки и Git.

~~~bash
systemctl status AmneziaVPN --no-pager
~~~

## Виртуальные машины на физических компьютерах

zet, nuc и 02i0132 включают libvirt, QEMU/KVM, virt-manager, virt-viewer, swtpm для виртуального TPM, virtiofsd для общих папок и SPICE USB. Пользователь nikolaev состоит в libvirtd и kvm. В UEFI нужна аппаратная виртуализация.

~~~bash
ls -l /dev/kvm
id
virsh -c qemu:///system list --all
virsh -c qemu:///system net-list --all
~~~

Если сеть default существует, но остановлена:

~~~bash
virsh -c qemu:///system net-autostart default
virsh -c qemu:///system net-start default
~~~

Не запускать net-start повторно для уже активной сети. Если default отсутствует, её нужно определить отдельно. В virt-manager выбирать **QEMU/KVM system**. Образы по умолчанию — /var/lib/libvirt/images; Data или games автоматически не используются.

Источники: [modules/gnome.nix](https://github.com/nikolaevmpf/gnome-config/blob/main/modules/gnome.nix), [профили](https://github.com/nikolaevmpf/gnome-config/tree/main/hosts).
