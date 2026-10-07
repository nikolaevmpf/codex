# vm — тестовая виртуальная машина

[[Linux/NixOS/Обзор|Все компьютеры]] · [[Linux/NixOS/Применение профиля|Первое применение]] · [[Linux/NixOS/Обновление и откат|Обновление]]

## Оглавление

- [[Linux/NixOS/Компьютеры/vm#Паспорт|Паспорт]]
- [[Linux/NixOS/Компьютеры/vm#Установка и первое применение|Установка и первое применение]]
- [[Linux/NixOS/Компьютеры/vm#Проверки после перезагрузки|Проверки после перезагрузки]]
- [[Linux/NixOS/Компьютеры/vm#Название и набор программ|Название и набор программ]]
- [[Linux/NixOS/Компьютеры/vm#Создание новой VM|Создание новой VM]]
- [[Linux/NixOS/Компьютеры/vm#Что проверять|Что проверять]]
- [[Linux/NixOS/Компьютеры/vm#Последующие изменения|Последующие изменения]]

## Паспорт

| Параметр | Значение |
| --- | --- |
| Модель | QEMU/KVM, UEFI, Virtio-диск; в установочном примере около 100 ГиБ |
| Графика | Virtio GPU (1af4:1050), штатный драйвер ядра и Mesa |
| Профиль | `vm` |
| Загрузка | UEFI, systemd-boot |
| EFI UUID | `EF55-A313` |
| Системный Btrfs UUID | `f5fb4ec1-a38e-4da6-b226-3749da38015e` |
| Пользователь | nikolaev |
| Ветка NixOS | nixos-26.05 |
| Состояние | Исходный тестовый профиль проекта. Его UUID относятся только к конкретной VM. |

UUID взяты из текущего профиля; после переустановки могут измениться. Один Btrfs содержит @root → /, @home → /home, @nix → /nix, @log → /var/log. EFI монтируется в /boot.

## Установка и первое применение

При установке с нуля сначала [[Linux/NixOS/Установка|базовая установка]]. На установленной системе не запускать Disko.

Получить ~/gnome-config по [[Linux/NixOS/Применение профиля|общей инструкции]], сверить UUID и создать lock-файл. Затем:

~~~bash
cd ~/gnome-config
lsblk -f
findmnt -t btrfs -o TARGET,SOURCE,OPTIONS
findmnt /boot
sudo nixos-rebuild build --flake "path:$PWD#vm"
~~~

Только после успешной сборки:

~~~bash
sudo nixos-rebuild boot --flake "path:$PWD#vm"
sudo reboot
~~~

До первой загрузки профиля использовать именно vm, даже если базовая система называется nixos.

## Проверки после перезагрузки

~~~bash
hostname
nixos-version
systemctl --failed
findmnt -t btrfs -o TARGET,SOURCE,OPTIONS
findmnt /boot
lspci -nnk
~~~


## Название и набор программ

Flake-профиль vm, hostname **nixos**. Общая команда nix-update преобразует hostname nixos в профиль vm.

GNOME/GDM, Firefox, Ghostty, Dock, темы, SSH, PipeWire и очистка включены. **Steam и GameMode отсутствуют.** NVIDIA и профиль хоста KVM не нужны: VM является гостем.

Включено services.qemuGuest.enable. В настройках гипервизора нужен канал org.qemu.guest_agent.0:

~~~bash
systemctl status qemu-guest-agent --no-pager
lspci -nnk | grep -A3 -E 'VGA|Display'
~~~

## Создание новой VM

Создать виртуальный диск, UEFI-загрузку и совместимые Virtio-устройства. При установке через Minimal ISO контролировать RAM и overlay /nix/store. В прежней попытке 4 ГиБ RAM не хватило для окружения установки; увеличить память и проверить место, а не форматировать диск повторно вслепую.

После установки новой VM UUID будут другими. Обновить **hosts/vm/default.nix** до применения профиля. При создании второй отдельной VM предпочтительно добавить самостоятельный профиль, чтобы не заменить UUID первой.

## Что проверять

Сеть, GNOME, звук, изменение разрешения, agent, точку EFI и четыре подтома Btrfs. Отсутствие NVIDIA и Steam здесь ожидаемо. В меню systemd-boot хранится до десяти поколений.


## Последующие изменения

~~~bash
nix-update
~~~

Для обновления пакетов:

~~~bash
nix-update --upgrade
~~~

Запускать от nikolaev, без sudo. После обновления ядра или драйвера перезагрузиться. Lock-файл и локальные правки сохранять осознанно: [[Linux/NixOS/Обновление и откат|подробности]].

Источник: [профиль vm](https://github.com/nikolaevmpf/gnome-config/tree/main/hosts/vm), [общий flake](https://github.com/nikolaevmpf/gnome-config/blob/main/flake.nix).
