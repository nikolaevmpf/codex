# VirtualBox на Arch Linux

## Установка на хост

Для стандартного ядра `linux`:

```bash
sudo pacman -Syu virtualbox virtualbox-host-modules-arch
```

Для другого ядра используйте DKMS и заголовки **своего** ядра. Пример для `linux-lts`:

```bash
sudo pacman -Syu virtualbox virtualbox-host-dkms linux-lts-headers
```

Выберите один вариант. После обновления ядра перезагрузитесь, прежде чем загружать модули.

```bash
sudo modprobe vboxdrv
sudo usermod -aG vboxusers "$USER"  # Доступ гостя к USB
```

Выйдите и войдите снова, затем запустите `virtualbox`.

## Extension Pack — необязательно

Для функций расширения проверьте условия лицензии и совпадение версии с VirtualBox:

```bash
yay -S virtualbox-ext-oracle
```

## Проверка

```bash
lsmod | grep vbox
dkms status  # Только для варианта с DKMS
```

DKMS обычно пересобирает модули автоматически. При ошибке проверьте заголовки ядра и журнал сборки. Для NAT Network не нужно включать `systemd-networkd`.

## Внутри гостевой Arch Linux

```bash
sudo pacman -Syu virtualbox-guest-utils
sudo systemctl enable --now vboxservice.service
```

Для гостя без графики используйте `virtualbox-guest-utils-nox` вместо графического пакета.
