# Astra Linux: Ansible

## Установка

```bash
sudo apt update
sudo apt install ansible
ansible --version
```

SSH-ключи предпочтительнее паролей; см. [[ssh]]. `sshpass` нужен только для подключения по паролю:

```bash
sudo apt install sshpass
```

## Проверка узла

```bash
# Заменить имя пользователя и адрес; запятая задаёт inline inventory
ansible all -i '192.168.1.100,' -u username -m ping
```

Модуль `ping` проверяет SSH и Python на удалённом узле, а не ICMP.
