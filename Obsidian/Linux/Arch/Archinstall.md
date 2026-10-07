# Arch Linux: установка через archinstall

[[Содержание|Главное оглавление]] · [[Linux/Arch/Обзор|Arch Linux]]

Рабочий стол — GNOME. Ручная установка: [[Linux/Arch/Install|Ручная установка]]. Настройки рабочего стола: [[Linux/Arch/Gnome|Arch Linux: GNOME]].

## Оглавление заметки

- [[Linux/Arch/Archinstall#Загрузочная флешка|Загрузочная флешка]]
- [[Linux/Arch/Archinstall#В установочном образе|В установочном образе]]
- [[Linux/Arch/Archinstall#После установки|После установки]]
- [[Linux/Arch/Archinstall#AUR: установка yay|AUR: установка yay]]
- [[Linux/Arch/Archinstall#Zsh — необязательно|Zsh — необязательно]]
- [[Linux/Arch/Archinstall#Проверка|Проверка]]

## Загрузочная флешка

Скачайте ISO с [archlinux.org](https://archlinux.org/download/) и проверьте подпись по инструкции на сайте.

```bash
# Найти флешку и размонтировать её разделы перед записью
lsblk -o NAME,SIZE,MODEL,TRAN,MOUNTPOINTS

# Заменить оба пути. /dev/sdX — устройство целиком, не раздел
sudo dd if=/path/to/archlinux.iso of=/dev/sdX bs=4M status=progress conv=fsync
sync
```

> [!warning] Данные будут удалены
> Запись ISO стирает содержимое флешки. Форматирование в установщике стирает выбранные разделы. Сначала сделайте резервную копию.

## В установочном образе

Загрузитесь в режиме UEFI. Команды выполняются от root, без sudo.

```bash
loadkeys ru
setfont cyr-sun16
# Для крупного шрифта: setfont ter-c32b
# Вернуть латинскую раскладку: loadkeys us
ls /sys/firmware/efi/efivars  # Проверка UEFI
timedatectl set-ntp true
```

Для Wi-Fi см. [[Linux/Arch/wifi|Wi-Fi: подключение]]. При проводном подключении сеть обычно настраивается автоматически.

```bash
ping -c 3 archlinux.org
archinstall
```

Если ping не отвечает, проверьте сеть и DNS; ICMP также может быть заблокирован.

| Пункт | Что выбрать |
| --- | --- |
| Disk configuration | Нужный диск; проверить итоговую разметку |
| Bootloader | Для UEFI — systemd-boot или GRUB |
| User account | Обычный пользователь с правами sudo |
| Profile | Desktop → GNOME |
| Graphics driver | Драйвер своей видеокарты |
| Network configuration | NetworkManager |
| Timezone | Europe/Moscow или свой часовой пояс |
| Audio | PipeWire |

При dual boot сохраните разделы другой системы и существующий EFI-раздел. Проверьте сводку, запустите установку, затем перезагрузитесь без флешки.

## После установки

Далее — терминал установленной системы, обычный пользователь.

```bash
sudo pacman -Syu
sudo pacman -S --needed base-devel git ncdu mc bash-completion btop nmap \
  vi cronie rsync ufw papirus-icon-theme transmission-gtk obsidian telegram-desktop

# Только если нужны задания cron
sudo systemctl enable --now cronie.service
```

Не используйте `pacman -Sy` отдельно: Arch не поддерживает частичные обновления.

### Удаление ненужного

```bash
# Проверить наличие пакетов
pacman -Q epiphany gnome-connections gnome-tour gnome-software \
  simple-scan malcontent gnome-maps htop

# Пример: удалять только установленные и ненужные пакеты
sudo pacman -Rs epiphany gnome-tour
```

Перед подтверждением проверьте зависимости. Межсетевой экран: [[Linux/Utility/ufw|UFW: межсетевой экран]].

## AUR: установка yay

Сборка выполняется обычным пользователем. Прочитайте `PKGBUILD` и вспомогательные файлы; проверки подписей и контрольных сумм оставляйте включёнными.

```bash
mkdir -p ~/builds
cd ~/builds
git clone https://aur.archlinux.org/yay.git
cd yay
less PKGBUILD
makepkg -si --needed
cd ~

# Необязательные программы и темы
yay -S --needed google-chrome bibata-cursor-theme gnome-shell-extension-dash-to-dock
```

Если каталог `~/builds/yay` уже есть, проверьте его перед обновлением. Ошибки PGP исправляйте проверкой ключа автора, а не `--skippgpcheck`.

## Zsh — необязательно

```bash
sudo pacman -S --needed zsh curl zsh-completions \
  zsh-autosuggestions zsh-syntax-highlighting
chsh -s /usr/bin/zsh
```

Для Oh My Zsh сначала сохраните свой `.zshrc`, скачайте и прочитайте установщик:

```bash
curl -fL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh \
  -o /tmp/oh-my-zsh-install.sh
less /tmp/oh-my-zsh-install.sh
RUNZSH=no CHSH=no sh /tmp/oh-my-zsh-install.sh
```

Если задан `ZDOTDIR`, проверьте, какой файл изменяет установщик. В `${ZDOTDIR:-$HOME}/.zshrc` замените значение темы Oh My Zsh на `ZSH_THEME="arrow"`.

Добавьте один раз в конец файла, после инициализации Oh My Zsh:

```zsh
alias upd='sudo pacman -Syu'
alias df='df -h'
source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
# Подсветка подключается последней
source /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

Без Oh My Zsh перед подключением плагинов добавьте:

```zsh
autoload -Uz compinit
compinit
```

Выйдите из сеанса и войдите снова. В уже запущенном Zsh настройки перечитываются так:

```zsh
source "${ZDOTDIR:-$HOME}/.zshrc"
```

## Проверка

```bash
systemctl --failed
nmcli general status
timedatectl status
sudo ufw status verbose
zsh --version
```

Журнал проблемной службы: `journalctl -b -u ИМЯ_СЛУЖБЫ`.

[ArchWiki: archinstall](https://wiki.archlinux.org/title/Archinstall)
