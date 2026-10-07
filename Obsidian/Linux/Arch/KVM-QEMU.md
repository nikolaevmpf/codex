# KVM / QEMU

Виртуальные машины Linux с аппаратной виртуализацией. В UEFI должны быть включены Intel VT-x или AMD-V.

## Хост Arch Linux

```bash
sudo pacman -Syu
sudo pacman -S --needed qemu-full libvirt virt-manager dnsmasq
sudo systemctl enable --now libvirtd.service
sudo usermod -aG libvirt "$USER"
```

Выйдите и войдите снова. В `virt-manager` используйте подключение **QEMU/KVM system** (`qemu:///system`).

```bash
sudo virsh -c qemu:///system net-list --all
# Если сеть default есть, но не запущена:
sudo virsh -c qemu:///system net-start default
sudo virsh -c qemu:///system net-autostart default
```

## Агент внутри гостевой ОС

Arch Linux:

```bash
sudo pacman -S --needed qemu-guest-agent
sudo systemctl enable --now qemu-guest-agent.service
```

Ubuntu / Debian:

```bash
sudo apt install qemu-guest-agent
sudo systemctl enable --now qemu-guest-agent.service
```

В настройках ВМ нужен канал `org.qemu.guest_agent.0`. При отсутствии канала агент не запустится.

## UFW — при проблемах с сетью гостя

Для примера с мостом `virbr0` и внешним интерфейсом `enp1s0`:

```bash
sudo ufw allow in on virbr0 to any port 53
sudo ufw allow in on virbr0 to any port 67 proto udp
sudo ufw route allow in on virbr0 out on enp1s0
```

Замените интерфейсы своими. Если сеть уже работает, дополнительные правила не нужны.

## Экспорт ВМ

Полностью выключите ВМ. Пример предполагает один диск; проверьте остальные диски и снимки.

```bash
sudo virsh -c qemu:///system domstate win11
sudo virsh -c qemu:///system domblklist win11 --details
mkdir -p /mnt/Data/VM/Win11
sudo virsh -c qemu:///system dumpxml win11 > /mnt/Data/VM/Win11/win11.xml
sudo cp /var/lib/libvirt/images/win11.qcow2 /mnt/Data/VM/Win11/
```

Для UEFI отдельно сохраните файл NVRAM из XML. Образы с backing file требуют сохранения всей цепочки.

## Импорт ВМ

```bash
sudo cp /mnt/Data/VM/Win11/win11.qcow2 /var/lib/libvirt/images/
# Сначала проверить пути дисков и NVRAM в XML
sudo virsh -c qemu:///system define /mnt/Data/VM/Win11/win11.xml
```

Владельца диска задавайте по конфигурации QEMU хоста: имя `libvirt-qemu` из Debian не универсально для Arch.

## Установка по сети

В virt-manager выберите источник **Network install (URL)**. Пример для Debian 13:

```text
https://deb.debian.org/debian/dists/trixie/main/installer-amd64/
```
