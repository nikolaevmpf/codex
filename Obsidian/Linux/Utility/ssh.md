### Cоздание ключа
	ssh-keygen -t rsa

### Копирование ключа на удаленный хост
	ssh-copy-id <имя пользователя>@<хост>

### Установка SSH server
	sudo pacman -S openssh
	sudo systemctl enable --now sshd
