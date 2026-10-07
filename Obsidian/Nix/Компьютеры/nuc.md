# nuc — Intel NUC8i7HVK

[[Nix/Обзор|Все компьютеры]] · [[Nix/Применение профиля|Первое применение]] · [[Nix/Обновление и откат|Обновление]]

## Оглавление

- [[Nix/Компьютеры/nuc#Паспорт|Паспорт]]
- [[Nix/Компьютеры/nuc#Установка и первое применение|Установка и первое применение]]
- [[Nix/Компьютеры/nuc#Проверки после перезагрузки|Проверки после перезагрузки]]
- [[Nix/Компьютеры/nuc#Два накопителя|Два накопителя]]
- [[Nix/Компьютеры/nuc#Графика и оборудование|Графика и оборудование]]
- [[Nix/Компьютеры/nuc#Рабочие приложения|Рабочие приложения]]
- [[Nix/Компьютеры/nuc#KVM|KVM]]
- [[Nix/Компьютеры/nuc#Ghostty и локальная сеть|Ghostty и локальная сеть]]
- [[Nix/Компьютеры/nuc#Последующие изменения|Последующие изменения]]

## Паспорт

| Параметр | Значение |
| --- | --- |
| Модель | Intel NUC8i7HVK, Core i7-8809G, 32 ГБ RAM |
| Графика | Intel HD 630 (i915) и Radeon RX Vega M GH (amdgpu) |
| Профиль | `nuc` |
| Загрузка | UEFI, systemd-boot |
| EFI UUID | `2AE2-E8A4` |
| Системный Btrfs UUID | `fc64e8f1-e978-4846-87c6-18308d6a81ce` |
| Пользователь | nikolaev |
| Ветка NixOS | nixos-26.05 |
| Состояние | Профиль подготовлен; успешная сборка и работа всех устройств на NUC ещё требуют подтверждения. |

UUID взяты из текущего профиля; после переустановки могут измениться. Один Btrfs содержит @root → /, @home → /home, @nix → /nix, @log → /var/log. EFI монтируется в /boot.

## Установка и первое применение

При установке с нуля сначала [[Nix/Установка|базовая установка]]. На установленной системе не запускать Disko.

Получить ~/gnome-config по [[Nix/Применение профиля|общей инструкции]], сверить UUID и создать lock-файл. Затем:

~~~bash
cd ~/gnome-config
lsblk -f
findmnt -t btrfs -o TARGET,SOURCE,OPTIONS
findmnt /boot
sudo nixos-rebuild build --flake "path:$PWD#nuc"
~~~

Только после успешной сборки:

~~~bash
sudo nixos-rebuild boot --flake "path:$PWD#nuc"
sudo reboot
~~~

До первой загрузки профиля использовать именно nuc, даже если базовая система называется nixos.

## Проверки после перезагрузки

~~~bash
hostname
nixos-version
systemctl --failed
findmnt -t btrfs -o TARGET,SOURCE,OPTIONS
findmnt /boot
lspci -nnk
~~~


## Два накопителя

| Диск | Файловая система | Назначение |
| --- | --- | --- |
| NVMe около 232,9 ГиБ | EFI + Btrfs | Системный, при первоначальной установке очищался по плану |
| NVMe около 931,5 ГиБ | ext4, Data | Сохранить данные, не форматировать |

Data UUID: `0ae52be7-d75e-4044-8367-a8687251a8cb`. Монтирование /mnt/Data с noatime, nofail, automount и таймаутом 5 секунд. Профиль не меняет права и содержимое Data.

~~~bash
lsblk -f
ls /mnt/Data
findmnt -T /mnt/Data -o TARGET,SOURCE,FSTYPE
~~~

Убедиться, что фактическая файловая система ext4 с ожидаемым UUID. Имена nvme0n1/nvme1n1 не фиксированы; при переустановке выбирать системный диск по модели, размеру и серийному номеру.

## Графика и оборудование

hardware.nix включает i915 и amdgpu в initrd, 32-битную графику, firmware, microcode Intel и Bluetooth. В аппаратной памятке: Intel I219-LM/I210 Ethernet, Intel Wireless 8265.

~~~bash
lspci -nnk | grep -A3 -E 'VGA|3D|Display|Ethernet|Network'
nmcli device status
bluetoothctl show
~~~

NVIDIA-модули этому компьютеру не нужны. Работу выходов обеих GPU, Wi-Fi, Bluetooth, звука и сна проверить после первой сборки.

## Рабочие приложения

Steam, GameMode, LibreOffice, Telegram, MAX через Flatpak, Obsidian, Pinta, Remmina, **VS Code**, Transmission, Amnezia VPN. Автовход nikolaev включён.

MAX устанавливает install-max.service, но обновляется отдельно через Flatpak. Импорт VPN-конфигурации выполняется пользователем. См. [[Nix/GNOME и приложения|общую заметку]].

Если загрузка VS Code с Microsoft обрывается, проверить сеть/VPN. Временное исключение пакета требует редактирования hosts/nuc/apps.nix и новой сборки, это не автоматическая часть установки.

## KVM

Включён kvm-intel, libvirt, QEMU, virt-manager, swtpm, virtiofsd и SPICE USB. В UEFI включить Intel Virtualization Technology.

Проверки и сеть: [[Nix/GNOME и приложения#Виртуальные машины на физических компьютерах|QEMU/KVM]]. Образы по умолчанию находятся на системном диске в /var/lib/libvirt/images; на Data они автоматически не перенесены.

## Ghostty и локальная сеть

JetBrains Mono 12, чёрный фон, окно 170 × 45; конфигурация /etc/ghostty/nuc.conf.

Записи hosts: zet → 192.168.1.59, nuc → 192.168.1.149. Они не назначают статический IP, сеть управляется NetworkManager.


## Последующие изменения

~~~bash
nix-update
~~~

Для обновления пакетов:

~~~bash
nix-update --upgrade
~~~

Запускать от nikolaev, без sudo. После обновления ядра или драйвера перезагрузиться. Lock-файл и локальные правки сохранять осознанно: [[Nix/Обновление и откат|подробности]].

Источник: [профиль nuc](https://github.com/nikolaevmpf/gnome-config/tree/main/hosts/nuc), [общий flake](https://github.com/nikolaevmpf/gnome-config/blob/main/flake.nix).
