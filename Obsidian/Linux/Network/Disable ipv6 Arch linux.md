# Arch Linux: отключение IPv6 через GRUB

Только для систем с GRUB. Отключайте IPv6, если он действительно мешает работе сети.

## Параметр ядра

```bash
sudo nano /etc/default/grub
```

Добавьте `ipv6.disable=1` к **существующим** параметрам, не удаляя остальные. Пример:

```ini
GRUB_CMDLINE_LINUX_DEFAULT="quiet ipv6.disable=1"
```

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
sudo reboot
```

Редактирование `/etc/hosts` само по себе IPv6 не отключает.

## Проверка и отмена

```bash
cat /proc/cmdline
ip -6 address
```

Для отмены удалите параметр, снова сгенерируйте конфигурацию GRUB и перезагрузитесь.
