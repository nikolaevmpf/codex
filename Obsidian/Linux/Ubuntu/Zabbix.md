Ubuntu 24.04 LTS Zabbix 7.0

sudo nano /etc/apt/apt.conf.d/80proxy

	Acquire::http::proxy "http://10.2.1.252:8080/";
	Acquire::https::proxy "http://10.2.1.252:8080/";
	Acquire::ftp::proxy "ftp://10.2.1.252:8080/";

sudo nano /etc/wgetrc

	use_proxy = on
	http_proxy = http://10.2.1.252:8080/ 
	https_proxy = http://10.2.1.252:8080/ 
	ftp_proxy = [http://10.2.1.252:8080/](http://10.2.1.243:8080/)

  

wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_7.0-1+ubuntu24.04_all.deb
sudo dpkg -i zabbix-release_7.0-1+ubuntu24.04_all.deb
sudo apt update

sudo apt install postgresql postgresql-contrib 

sudo apt install zabbix-server-pgsql zabbix-frontend-php php8.3-pgsql zabbix-nginx-conf zabbix-sql-scripts zabbix-agent

sudo -u postgres createuser --pwprompt zabbix
sudo -u postgres createdb -O zabbix zabbix

sudo zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix

sudo nano /etc/zabbix/zabbix_server.conf

	DBPassword=zabbix

sudo nano /etc/zabbix/nginx.conf

	listen 80;
	server_name zabbix;

sudo locale-gen ru_RU

sudo systemctl enable zabbix-server zabbix-agent nginx php8.3-fpm
sudo shutdown -r now

[http://ip adderss/zabbix]

Database password: password
login: Admin
password: zabbix