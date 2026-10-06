**Плюсы:**

- Нативная производительность (почти без оверхеда)
- Поддержка аппаратной виртуализации (Intel VT-x / AMD-V)
- Хорошая интеграция с Linux
- Подходит для серьезного тестирования

**Установка:**

```
sudo pacman -S qemu-full libvirt virt-manager dnsmasq
sudo systemctl enable --now libvirtd
sudo usermod -aG kvm,libvirt nikolaev
```

**Установка агента:**

```
sudo pacman -S qemu-guest-agent
sudo systemctl enable --now qemu-guest-agent
```

```
sudo apt install qemu-guest-agent
sudo systemctl enable --now qemu-guest-agent
```

**Правила для ufw:**

```
sudo ufw allow in on virbr0
sudo ufw allow out on virbr0
```
# Экспорт вм

Копирование файла конфигурации

	sudo virsh dumpxml win11 > /mnt/Data/VM/Win11/win11.xml

Копирование образ диска

	sudo cp /var/lib/libvirt/images/win11.qcow2 /mnt/Data/VM/Win11


# Импорт вм

Копирование образ диска

	sudo cp /mnt/Data/VM/Win11/win11.qcow2 /var/lib/libvirt/images
	sudo chown libvirt-qemu:libvirt-qemu /var/lib/libvirt/images/win11.qcow2

Копирование файла конфигурации

	sudo virsh define /mnt/Data/VM/Win11/win11.xml

### Установка vm через интернет
Просто вставить URL в при выборе iso.

Debian 13
https://deb.debian.org/debian/dists/trixie/main/installer-amd64/
