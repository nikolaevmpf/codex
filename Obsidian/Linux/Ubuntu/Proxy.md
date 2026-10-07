# Ubuntu: прокси

[[Содержание|Главное оглавление]] · [[Linux/Ubuntu/Обзор|Ubuntu]]

Пример адреса: `http://10.2.1.252:8080/`. Замените его своим. HTTP-прокси может обслуживать HTTPS через CONNECT — это не требует схемы `https://` в адресе прокси.

## Текущая сессия Bash

Добавьте функции один раз в `~/.bashrc`:

```bash
setproxy() {
  export http_proxy="http://10.2.1.252:8080/"
  export https_proxy="$http_proxy"
  export ftp_proxy="$http_proxy"
  export no_proxy="localhost,127.0.0.1,::1,.gz.local"
  export HTTP_PROXY="$http_proxy" HTTPS_PROXY="$https_proxy"
  export FTP_PROXY="$ftp_proxy" NO_PROXY="$no_proxy"
}
unsetproxy() {
  unset http_proxy https_proxy ftp_proxy no_proxy
  unset HTTP_PROXY HTTPS_PROXY FTP_PROXY NO_PROXY
}
```

```bash
source ~/.bashrc
setproxy    # Включить
unsetproxy  # Отключить
```

## Постоянные переменные

В `/etc/environment` используйте пары `имя=значение`, **без `export`**:

```ini
http_proxy="http://10.2.1.252:8080/"
https_proxy="http://10.2.1.252:8080/"
no_proxy="localhost,127.0.0.1,::1,.gz.local"
```

Применяются при новом входе. Системным службам могут потребоваться отдельные настройки прокси.

## APT

Файл `/etc/apt/apt.conf.d/80proxy`:

```text
Acquire::http::Proxy "http://10.2.1.252:8080/";
Acquire::https::Proxy "http://10.2.1.252:8080/";
```

## Wget

Файл `~/.wgetrc` для пользователя или `/etc/wgetrc` для системы:

```ini
use_proxy = on
http_proxy = http://10.2.1.252:8080/
https_proxy = http://10.2.1.252:8080/
ftp_proxy = http://10.2.1.252:8080/
```

Не сохраняйте логины и пароли прокси в общей заметке. Для отключения удалите только добавленные параметры из соответствующих файлов.

## Связанные заметки

- [[Linux/Ubuntu/Zabbix|Ubuntu 24.04: Zabbix 7.0 + PostgreSQL]]
- [[Linux/Utility/ufw|UFW: межсетевой экран]]
