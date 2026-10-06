sudo pacman -S tftpd-hpa

sudo nano /etc/conf.d/tftpd

	TFTPD_ARGS="--secure -4 /srv/tftp"
	TFTPD_USER="nobody"
	TFTPD_GROUP="nobody"

- --secure /srv/tftp: Указывает каталог, который будет использоваться для хранения файлов TFTP. В данном случае это /srv/tftp.
-  -4 указывает на использование только ipv4 (необходимо при отключении ipv6)
- TFTPD_USER и TFTPD_GROUP: Указывают пользователя и группу, от имени которых будет работать сервер.

sudo mkdir -p /srv/tftp
sudo chown -R nobody:nobody /srv/tftp
sudo chmod -R 777 /srv/tftp  # Разрешите чтение и запись для всех (для тестирования)

sudo systemctl start tftpd
sudo systemctl enable tftpd