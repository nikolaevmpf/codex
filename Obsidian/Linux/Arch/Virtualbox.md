Установка VirtualBox на Arch Linux включает несколько шагов: установка основных пакетов, настройка ядра и добавление пользователя в группу `vboxusers`. Вот пошаговая инструкция:

---

### 1. **Установка VirtualBox и зависимостей**
Откройте терминал и выполните:

```bash
sudo pacman -Syu virtualbox virtualbox-host-dkms
```
- `virtualbox` — основной пакет VirtualBox.
- `virtualbox-host-dkms` — модули ядра для VirtualBox (необходимы, если вы используете нестандартное ядро).

Если у вас **стандартное ядро Linux (linux)**, можно установить `virtualbox-host-modules-arch` вместо `virtualbox-host-dkms`:

```bash
sudo pacman -Syu virtualbox virtualbox-host-modules-arch
```

---

### 2. **Установка расширений (опционально)**
Для поддержки USB 2.0/3.0, RDP и других функций установите `virtualbox-ext-oracle` из AUR:

```bash
yay -S virtualbox-ext-oracle
```
(Или используйте другой AUR-хелпер, например `paru`).

---

### 3. **Загрузка модулей ядра**
VirtualBox требует загрузки модуля `vboxdrv`. Выполните:

```bash
sudo modprobe vboxdrv
```
Чтобы модуль загружался автоматически при запуске, добавьте его в `/etc/modules-load.d/virtualbox.conf`:

```bash
echo "vboxdrv" | sudo tee /etc/modules-load.d/virtualbox.conf
```

---

### 4. **Добавление пользователя в группу `vboxusers`**
Для доступа к USB-устройствам из виртуальных машин добавьте себя в группу `vboxusers`:

```bash
sudo usermod -aG vboxusers $USER
```
После этого **перезагрузите систему** или выйдите/войдите заново.

---

### 5. **Запуск VirtualBox**
После перезагрузки запустите VirtualBox из меню приложений или через терминал:

```bash
virtualbox
```

---

### 6. **Дополнительные настройки (если нужно)**
- **Если VirtualBox не запускается**, проверьте, что модули ядра загружены:
  ```bash
  lsmod | grep vbox
  ```
  Должны быть видны `vboxdrv`, `vboxnetadp`, `vboxnetflt`.

- **Для поддержки NAT Network** может потребоваться включить службу:
  ```bash
  sudo systemctl enable --now systemd-networkd
  ```

---

### **Важно!**
- Если вы обновляете ядро, пересоберите модули VirtualBox:
  ```bash
  sudo dkms install vboxhost/$(pacman -Q virtualbox-host-dkms | awk '{print $2}' | sed 's/-.*//')-ARCH
  ```
- Для **гостевых ОС** установите `virtualbox-guest-utils` (в гостевой системе).

---

Готово! Теперь вы можете создавать и запускать виртуальные машины в VirtualBox на Arch Linux.


# Virtualbox tools
	sudo pacman -S virtualbox-guest-utils
	sudo systemctl enable vboxservice.service
	sudo reboot