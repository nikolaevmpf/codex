Краткая инструкция о том, как избавиться от назойливого cloud-init в Ubuntu Server, который постоянно напоминает о себе в консоли в виде ненужных и бесполезных сообщений и неслабо тормозит систему.

Открываем файл /etc/cloud/cloud.cfg.d/90_dpkg.cfg

	sudo nano /etc/cloud/cloud.cfg.d/90_dpkg.cfg
В нём есть строка, которая начинается с datasource_list…

Приведём её в такой вид:

	datasource_list: [ None ]
Затем переконфигурируем пакет cloud-init

	sudo dpkg-reconfigure cloud-init
и избавляемся от него

	sudo apt purge cloud-init
Теперь удалим 2 директории: /etc/cloud/ и /var/lib/cloud/

	sudo rm -rf /etc/cloud/
	sudo rm -rf /var/lib/cloud/
Перезапускаем систему

	sudo shutdown -r now
и видим, что сообщений от cloud-init больше нет.