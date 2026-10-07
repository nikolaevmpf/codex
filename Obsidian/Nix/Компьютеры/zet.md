# zet — домашний игровой компьютер

[[Nix/Обзор|Все компьютеры]] · [[Nix/Применение профиля|Первое применение]] · [[Nix/Обновление и откат|Обновление]]

## Оглавление

- [[Nix/Компьютеры/zet#Паспорт|Паспорт]]
- [[Nix/Компьютеры/zet#Установка и первое применение|Установка и первое применение]]
- [[Nix/Компьютеры/zet#Проверки после перезагрузки|Проверки после перезагрузки]]
- [[Nix/Компьютеры/zet#Игровой диск|Игровой диск]]
- [[Nix/Компьютеры/zet#NVIDIA и сон|NVIDIA и сон]]
- [[Nix/Компьютеры/zet#MOZA R3|MOZA R3]]
- [[Nix/Компьютеры/zet#Программы и вход|Программы и вход]]
- [[Nix/Компьютеры/zet#Локальная сеть|Локальная сеть]]
- [[Nix/Компьютеры/zet#Последующие изменения|Последующие изменения]]

## Паспорт

| Параметр | Значение |
| --- | --- |
| Модель | ZET Gaming WARD H264 |
| Графика | NVIDIA GeForce RTX 4060 Ti, AD106 |
| Профиль | `zet` |
| Загрузка | UEFI, systemd-boot |
| EFI UUID | `1B70-3CD2` |
| Системный Btrfs UUID | `7bcd30d7-6ded-48b4-b17d-1160840adb72` |
| Пользователь | nikolaev |
| Ветка NixOS | nixos-26.05 |
| Состояние | Профиль используется в проекте; проверка восстановления после сна остаётся открытой. |

UUID взяты из текущего профиля; после переустановки могут измениться. Один Btrfs содержит @root → /, @home → /home, @nix → /nix, @log → /var/log. EFI монтируется в /boot.

## Установка и первое применение

При установке с нуля сначала [[Nix/Установка|базовая установка]]. На установленной системе не запускать Disko.

Получить ~/gnome-config по [[Nix/Применение профиля|общей инструкции]], сверить UUID и создать lock-файл. Затем:

~~~bash
cd ~/gnome-config
lsblk -f
findmnt -t btrfs -o TARGET,SOURCE,OPTIONS
findmnt /boot
sudo nixos-rebuild build --flake "path:$PWD#zet"
~~~

Только после успешной сборки:

~~~bash
sudo nixos-rebuild boot --flake "path:$PWD#zet"
sudo reboot
~~~

До первой загрузки профиля использовать именно zet, даже если базовая система называется nixos.

## Проверки после перезагрузки

~~~bash
hostname
nixos-version
systemctl --failed
findmnt -t btrfs -o TARGET,SOURCE,OPTIONS
findmnt /boot
nvidia-smi
~~~


## Игровой диск

Отдельный Btrfs Data: UUID `20b6bd92-e375-402e-a18c-abe43cf65b79`, точка `/games`. В профиле: subvolid=5, compress=zstd, noatime, nofail, automount, ожидание устройства 5 секунд. Диск монтируется при обращении, загрузка не должна зависеть от его доступности.

После применения профиля активировать и проверить:

~~~bash
lsblk -f
ls /games
findmnt -T /games -o TARGET,SOURCE,FSTYPE
~~~

Продолжать только если источник — ожидаемый Btrfs-диск Data, а не autofs или системный раздел:

~~~bash
sudo install -d -o nikolaev -g users -m 0755 /games/SteamLibrary
~~~

В Steam: **Настройки → Хранилище → добавить /games/SteamLibrary** и сделать библиотекой по умолчанию. Иначе игры продолжат устанавливаться в домашний каталог. Повторно форматировать Data не нужно.

## NVIDIA и сон

Драйвер stable, открытые модули, modesetting. Включены powerManagement.enable и службы NVIDIA suspend/resume; kernelSuspendNotifier выключен. Сохранение видеопамяти направлено в /var/tmp.

После изменения параметров перезагрузиться. Свободное место в /var/tmp — не меньше VRAM плюс около 5%. Успешное возобновление требует проверки на реальной машине. [[Nix/Диагностика#NVIDIA и восстановление после сна|Проверки и журналы]].

## MOZA R3

Только этот профиль импортирует moza.nix: hid-universal-pidff, cdc_acm, uinput, Boxflat и его udev-правила.

После применения переподключить USB базы, включить её, открыть Boxflat от обычного пользователя:

~~~bash
boxflat
lsusb -d 346e:
lsmod | grep -E 'hid_universal_pidff|cdc_acm'
sudo journalctl -k -b --no-pager | grep -Ei 'moza|pidff|346e|ttyACM'
~~~

FFB зависит также от игры и Proton. Конфигурация ядра в профиле не закрепляет конкретную версию; фактическое ядро проверить через uname -r.

## Программы и вход

Steam, GameMode, Firefox, Ghostty, Transmission, Boxflat, virt-manager и virt-viewer. LibreOffice, MAX и Amnezia в текущем профиле zet не объявлены. Автовход nikolaev включён.

Ghostty: JetBrains Mono 12, чёрный фон, окно 170 × 45, отдельный файл /etc/ghostty/zet.conf.

## Локальная сеть

В networking.hosts записаны zet → 192.168.1.59 и nuc → 192.168.1.149. Это записи разрешения имён, **не назначение адресов интерфейсам**.


## Последующие изменения

~~~bash
nix-update
~~~

Для обновления пакетов:

~~~bash
nix-update --upgrade
~~~

Запускать от nikolaev, без sudo. После обновления ядра или драйвера перезагрузиться. Lock-файл и локальные правки сохранять осознанно: [[Nix/Обновление и откат|подробности]].

Источник: [профиль zet](https://github.com/nikolaevmpf/gnome-config/tree/main/hosts/zet), [общий flake](https://github.com/nikolaevmpf/gnome-config/blob/main/flake.nix).
