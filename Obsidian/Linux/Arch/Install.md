# Arch Linux: ручная установка UEFI

Краткий пример для **пустого диска `/dev/sda`**. Для NVMe имена будут другими. Автоматический вариант: [[Archinstall]].

> [!warning] Форматирование
> Проверьте диск через `lsblk` и сохраните данные. Пример не подходит для dual boot без изменения разметки.

## 1. Сеть и диск

В live-системе выполняйте команды от root. Подключение Wi-Fi: [[wifi]]. Прокси при необходимости: [[Proxy]].

```bash
ls /sys/firmware/efi/efivars  # Убедиться, что загрузились в UEFI
timedatectl set-ntp true
ping -c 3 archlinux.org
lsblk -o NAME,SIZE,MODEL,FSTYPE,MOUNTPOINTS
fdisk /dev/sda
```

В `fdisk` создайте GPT и три раздела; шпаргалка: [[fdisk]].

| Раздел | Пример размера | Тип |
| --- | --- | --- |
| `/dev/sda1` | 1 ГиБ | EFI System |
| `/dev/sda2` | По потребности | Linux swap |
| `/dev/sda3` | Остаток | Linux filesystem |

```bash
mkfs.fat -F 32 /dev/sda1
mkswap /dev/sda2
mkfs.ext4 /dev/sda3
swapon /dev/sda2
mount /dev/sda3 /mnt
mount --mkdir /dev/sda1 /mnt/boot
```

## 2. Базовая система

```bash
pacstrap -K /mnt base base-devel linux linux-firmware nano \
  grub efibootmgr sudo networkmanager openssh
# Выполнить один раз; при повторе проверить файл на дубликаты
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt
```

Для физического компьютера дополнительно установите `intel-ucode` или `amd-ucode` по производителю процессора.

## 3. Время, язык и имя

Далее — внутри `arch-chroot`.

```bash
ln -sf /usr/share/zoneinfo/Europe/Moscow /etc/localtime
hwclock --systohc
nano /etc/locale.gen
```

Раскомментируйте строки:

```text
ru_RU.UTF-8 UTF-8
en_US.UTF-8 UTF-8
```

```bash
locale-gen
echo 'LANG=ru_RU.UTF-8' > /etc/locale.conf
echo 'arch' > /etc/hostname
nano /etc/vconsole.conf
```

Содержимое `/etc/vconsole.conf`:

```ini
KEYMAP=ru
FONT=cyr-sun16
```

В `/etc/hosts` добавьте:

```text
127.0.0.1 localhost
::1       localhost
127.0.1.1 arch.localdomain arch
```

## 4. Пользователь и службы

Замените `username` своим именем пользователя.

```bash
passwd  # Пароль root
useradd -m -G wheel -s /bin/bash username
passwd username
EDITOR=nano visudo
```

В `sudoers` через `visudo` раскомментируйте:

```text
%wheel ALL=(ALL:ALL) ALL
```

```bash
systemctl enable NetworkManager.service
# Только если нужен удалённый доступ
# systemctl enable sshd.service
```

## 5. Загрузчик и перезагрузка

EFI-раздел уже смонтирован в `/boot`; повторно монтировать его не нужно.

```bash
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=Arch
grub-mkconfig -o /boot/grub/grub.cfg
exit
umount -R /mnt
reboot
```

После загрузки установите [[Gnome]] или другой рабочий стол. Виртуальные машины: [[KVM-QEMU]], [[Virtualbox]].

[ArchWiki: установка](https://wiki.archlinux.org/title/Installation_guide)
