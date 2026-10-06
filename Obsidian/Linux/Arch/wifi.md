# Настройка беспроводной сети

  ## Проверяем не заблокирован ли WiFi
```
rfkill
```
  
- Если видим что что заблокирован wlan,
  
```
ID TYPE      DEVICE      SOFT      HARD  
0 bluetooth hci0   unblocked unblocked  
1 wlan      phy0     blocked unblocked
```

  - … то выполняем команду

```
rfkill unblock wifi
```

- Теперь все OK

```
ID TYPE      DEVICE      SOFT      HARD  
0 bluetooth hci0   unblocked unblocked  
1 wlan      phy0   unblocked unblocked
```
## Утилита `iwctl` для работы с WiFi

```
iwctl
```

  В самой утилите `iwctl` вводим команды:

  - Смотрим ваши WiFi сетевые карты

```
[iwd]# device list
```

  `wlan0`

   Сканируем доступные сети

```
[iwd]# station wlan0 scan
```

- Выводим список доступных сетей

```
[iwd]# station wlan0 get-networks
```

- Например получаем такое, видим там свою сеть

```
                              Available networks
--------------------------------------------------------------------------------
  Network name                    Security          Signal
--------------------------------------------------------------------------------
  Ace                             psk               ****
  Nazok                           psk               ***
  Artem                           psk               ***
```
- Соединяемся с нашей сетью
```
[iwd]# station wlan0 connect Ace
```
- Вводим пароль
```
Type the network passphrase for Ace psk.
Passphrase: ********
```
- Выходим из `iwctl`
```
exit
```
