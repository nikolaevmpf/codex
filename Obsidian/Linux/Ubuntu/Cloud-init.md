# Ubuntu: отключение cloud-init

Отключайте только после завершения первоначальной настройки. В облаке cloud-init может управлять сетью, SSH-ключами и метаданными.

## Проверка

```bash
cloud-init status --long
```

Сначала проверьте, не зависит ли сеть от сгенерированных файлов в `/etc/netplan/`.

## Отключение без удаления

```bash
sudo touch /etc/cloud/cloud-init.disabled
sudo reboot
```

## Возврат

```bash
sudo rm /etc/cloud/cloud-init.disabled
sudo reboot
```

## Удаление — необязательно

```bash
sudo apt purge cloud-init
```

Проверьте список удаляемых пакетов. Не удаляйте `/etc/cloud` и `/var/lib/cloud` вручную без резервной копии: там сохраняются настройки и состояние.
