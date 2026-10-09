# Dell Latitude 3410

[[Linux/NixOS/Обзор|Все компьютеры]] · [[Linux/NixOS/Обновление и откат|Обновление]]

Профиль `dell`: Intel UHD Comet Lake-U (i915), Intel AX201 (iwlwifi), UEFI,
KIOXIA NVMe 256 ГБ. EFI UUID: `56FE-B413`; Btrfs UUID:
`2629ed44-c0b4-447f-b5bf-8df2f8bc698b`.
Подтома: @root → /, @home → /home, @nix → /nix, @log → /var/log.

Общие GNOME, Firefox, Ghostty, Steam и GameMode; LibreOffice, Transmission,
QEMU/KVM, libvirt, virt-manager, virt-viewer, swtpm, virtiofsd и SPICE USB.
Включены firmware, microcode Intel, Bluetooth, libinput и профили питания.
MOZA, NVIDIA и отдельный диск /games не подключены. Автовход не включён.

## Первое применение

Сверьте UUID с `lsblk -f`. Для новой рабочей копии:

```bash
git clone https://github.com/nikolaevmpf/gnome-config.git ~/gnome-config
cd ~/gnome-config
nix flake lock
sudo nixos-rebuild boot --flake "path:$PWD#dell"
```

Если репозиторий уже существует, выполните `git pull --ff-only` вместо клонирования.
После успешной сборки:

```bash
sudo reboot
```

До первого применения не используйте nix-update: базовый hostname nixos выбирает vm.
После перезагрузки проверьте:

```bash
hostname
systemctl --failed
findmnt -t btrfs -o TARGET,SOURCE
nmcli device status
ls -l /dev/kvm
virsh -c qemu:///system list --all
```

Последующие обновления: `nix-update`, версии пакетов: `nix-update --upgrade`.
В UEFI должна быть включена виртуализация Intel VT-x.
Проверьте звук, Bluetooth, тачпад, яркость, батарею и восстановление после сна.
Гибернация на дисковый swap не настроена; zram используется для обычного swap.
Конфигурация не форматирует диск. Сборка и работа на ноутбуке требуют проверки.
