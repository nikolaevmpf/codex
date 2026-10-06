sudo nano /etc/default/grub

GRUB_CMDLINE_LINUX_DEFAULT="ipv6.disable=1"

nano /etc/hosts

	#::1 localhost ip6-localhost ip6-loopback

sudo grub-mkconfig -o /boot/grub/grub.cfg

sudo reboot


