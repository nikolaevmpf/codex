# rsync: резервное копирование

[[Содержание|Главное оглавление]] · [[Linux/Utility/Обзор|Утилиты и сервисы Linux]]

`source/` копирует **содержимое** каталога. Без завершающего `/` копируется сам каталог. Регистр в путях важен: `Data` и `data` — разные имена.

## Сначала проверка

```bash
# -a: атрибуты и рекурсия, -v: подробности, -n: без записи
rsync -avn /mnt/Data/Foto/ /mnt/Elements/Foto/
```

Убедитесь, что накопитель смонтирован: `findmnt /mnt/Elements`. Иначе файлы могут попасть на системный диск.

## WD Elements

```bash
rsync -av /mnt/Data/Foto/ /mnt/Elements/Foto/
rsync -av /mnt/Data/Video/ /mnt/Elements/Video/
rsync -av /mnt/Data/DOC/ /mnt/Elements/DOC/
rsync -av /mnt/Data/Курсы/ /mnt/Elements/Курсы/
rsync -av /mnt/Data/Julia/ /mnt/Elements/Julia/
```

## Orico

```bash
rsync -av /mnt/Data/Foto/ /mnt/Orico/Foto/
rsync -av /mnt/Data/Video/ /mnt/Orico/Video/
rsync -av /mnt/Data/DOC/ /mnt/Orico/DOC/
rsync -av /mnt/Data/Курсы/ /mnt/Orico/Курсы/
rsync -av /mnt/Data/Julia/ /mnt/Orico/Julia/
```

Перед копированием проверьте `findmnt /mnt/Orico`. Эти команды не удаляют лишние файлы в назначении; `--delete` добавляйте только после dry run.

## Связанные заметки

- [[Linux/Utility/Vaultwarden|Vaultwarden: Docker + Nginx]]
- [[Linux/Utility/ufw|UFW: межсетевой экран]]
