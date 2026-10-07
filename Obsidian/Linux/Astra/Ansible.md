# Astra Linux: Ansible

[[Содержание|Главное оглавление]] · [[Linux/Astra/Обзор|Astra Linux]]

## Установка

```bash
sudo apt update
sudo apt install ansible
ansible --version
```

SSH-ключи предпочтительнее паролей; см. [[Linux/Utility/ssh|SSH: ключи и сервер]]. `sshpass` нужен только для подключения по паролю:

```bash
sudo apt install sshpass
```

## Проверка узла

```bash
# Заменить имя пользователя и адрес; запятая задаёт inline inventory
ansible all -i '192.168.1.100,' -u username -m ping
```

Модуль `ping` проверяет SSH и Python на удалённом узле, а не ICMP.
