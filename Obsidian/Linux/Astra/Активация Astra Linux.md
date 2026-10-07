# Astra Linux: активация через ЛСАЛ

ЛСАЛ — локальный сервер активации лицензий. Команды и структура конфигурации зависят от версии; перед выполнением проверьте `--help` и документацию своей поставки.

## Личный кабинет

В [lk.astra.ru](https://lk.astra.ru) откройте **Активация → Локальный сервер активации лицензий**:

1. Создайте подписку для ЛСАЛ.
2. Создайте подписку для клиентов.
3. Сохраните ID подписок и получите манифесты.

## Сервер ЛСАЛ

Установите пакет сервера из официального репозитория своей версии. В исходной заметке использовались инструменты `astra-lsla`:

```bash
astra-lsla --help
astra-lsla get-activation-manifest --help
astra-lsla set-credentials
```

Получите манифест способом из справки. Если требуется пароль, не сохраняйте его в заметке или истории команд. Каталог манифестов: `/var/opt/astra-lsla/manifest/`.

```bash
# Заменить значения своими; проверить параметры для своей версии
astra-lsla activate --server-name lsal-server
astra-lsla get-manifest --pool-id POOL_ID
```

## Клиент

Проверьте установленный вариант `astra-subscription`: desktop, server, embedded или mobile — в соответствии с лицензией.

```bash
astra-subscription status
sudo nano /etc/astra-subscription/common.yaml
```

В существующей конфигурации укажите сервер. Пример значений, не замена всего файла:

```yaml
baseurl: "https://lsal-server"
insecure: false
port: 8000
```

Проверьте `server.prefix` по документации своей версии. HTTPS-сертификат сервера должен быть доверенным на клиенте.

```bash
astra-subscription register
astra-subscription attach --pool-id POOL_ID
astra-subscription status
```

При регистрации введите учётные данные ЛСАЛ и ID организации из ответа сервера. Не используйте ID из чужого примера.
