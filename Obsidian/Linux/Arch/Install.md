lsip address add 10.2.93.232/24 dev ens33
ip route add default via 10.2.93.1

sudo nano  /etc/environment

	export http_proxy=http://10.2.1.252:8080/
	export https_proxy=http://10.2.1.252:8080/
	export ftp_proxy=http://10.2.1.252:8080/
	export rsync_proxy=http://10.2.1.252:8080/
	export no_proxy="localhost,127.0.0.1,localaddress,.localdomain.com"
## Manual install
[[fdisk]] /dev/sda

mkswap /dev/sda2
swapon /dev/sda2
mkfs.ext4 /dev/sda3
mount /dev/sda3 /mnt
mkfs.fat -F 32 /dev/sda1
mount  --mkdir /dev/sda1 /mnt/boot/

pacstrap -K /mnt base base-devel linux linux-firmware nano
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt
passwd
echo arch > /etc/hostname
nano /etc/hosts

	127.0.0.1 localhost
	::1 localhost
	127.0.0.1 arch.gz.local arch
ln -sf /usr/share/zoneinfo/Europe/Moscow /etc/localtime
hwclock --systohc
nano /etc/locale.gen

	ru_RU.UTF8 UTF8
	en_US.UTF8 UTF8
locale-gen
nano /etc/vconsole.conf

	KEYMAP=ru
	FONT=cyr-sun16
echo LANG="ru_RU.UTF-8"  > /etc/locale.conf
pacman-key --init
pacman-key --populate archlinux
nano /etc/pacman.conf

	[multilib]
	Include = /etc/pacman.d.mirrorlist
pacman -Sy
pacman -S grub efibootmgr sudo bash-completion openssh wget open-vm-tools qemu-base ncdu mc vim neofetch
nano /etc/sudoers

	%wheel ALL=(ALL:ALL) ALL
useradd -mg users -G wheel nikolaev
passwd nikolaev
systemctl enable systemd-networkd.service
systemctl enable systemd-resolved.service
systemctl enable vmtoolsd.service
systemctl enable vmware-vmblock-fuse.service
systemctl enable sshd.service
mount --mkdir /dev/sda1 /boot/efi
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg

exit
umount -R /mnt
reboot
