sudo pacman -S tigervnc
vncpasswd

Создайте файл `/home/$USERNAME/.vnc/config`с содержимым ( _замените $SESSION в соответствии с вашей установкой,_ например, _openbox_ , _plasma_ , _lxqt_ ).

ls /usr/share/xsessions             # Список доступных сессий
mkdir /home/$username/.vnc
nano ./.vnc/config

	session=$SESSION
	geometry=1280x720 
	dpi=96

Отредактируйте `/etc/tigervnc/vncserver.users`и добавьте, например `:4`, который в свою очередь будет соответствовать порту 5904, замените $USERNAME на имя пользователя, для которого вы только что создали пароль.

sudo nano /etc/tigervnc/vncserver.users

	:4=$USERNAME
sudo systemctl enable vncserver@:4
reboot