## Очистка кэша пакетного менеджера

	sudo pacman -Scc
	yay -Sc

## Удаление неиспользуемых пакетов

	sudo pacman -Rns $(pacman -Qtdq)

## Очистка кэша браузера

	rm -rf ~/.cache/chromium
	rm -rf ~/.cache/mozilla/firefox/*.default

## Очистка лог-файлов

	sudo truncate -s 0 /var/log/pacman.log

## Очистка директории /tmp

	sudo rm -rf /tmp/*

## Очистка кэша шрифтов

	fc-cache -frv
	sudo rm -rf ~/.cache/fontconfig/*