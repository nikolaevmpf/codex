sudo nano /etc/environment

	export http_proxy="10.2.1.252:8080"
	export https_proxy="10.2.1.252:8080"
	export no_proxy="localhost,127.0.0.1,::1"

##  Apt
sudo nano /etc/apt/apt.conf

	Acquire::http::Proxy "http://10.2.1.252:8080/";
	Acquire::https::Proxy "https://10.2.1.252:8080/";

## Wget
sudo nano /etc/wgetrc      (для всех)
sudo nano ~/.wgetrc        (для пользователя)

	use_proxy = on
	http_proxy = http://10.2.1.252:8080
	https_proxy = http://10.2.1.252:8080
	ftp_proxy = http://10.2.1.252:8080


** Если прокси-сервер требует аутентификации, добавьте [username]:[password]@ перед адресом прокси-сервера.

---
Можно также создать функции Bash для автоматической настройки proxy. Для этого добавить в файл ~/.bashrc следующий код:

	#Включить Proxy
	
	function setproxy()	 {
	    
	    export http_proxy="http://10.2.1.252:8080"
	    export https_proxy="https://10.2.1.252:8080"
	    export ftp_proxy="ftp://10.2.1.252:8080"
	
	}
	
	#Отключить Proxy
	
	function unsetproxy() {
	
	   unset {http,https,ftp}_proxy
	}

И применить сделанные настройки:

source ~/.bashrc

Теперь для быстрого включения и отключения прокси можно использовать команды **setproxy** и **unsetproxy**.