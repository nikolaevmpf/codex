
Kопирование файлов из папки источника(source) в папку назначения(destination)
Обязательно ставить / после каталока источника

Параметр n -это проверка


Копирование файлов на WD Elements из /mnt/data (DOC,Foto,Video)


`rsync -av /mnt/Data/Foto/ /mnt/Elements/Foto`
`rsync -av /mnt/Data/Video/ /mnt/Elements/Video`
`rsync -av /mnt/Data/DOC/ /mnt/Elements/DOC`
`rsync -av /mnt/Data/Курсы/ /mnt/Elements/Курсы`
`rsync -av /mnt/Data/Julia/ /mnt/Elements/Julia`

Копирование файлов на Orico из /mnt/data (DOC,Foto,Video)

`rsync -av /mnt/Data/Foto/ /mnt/Orico/Foto`
`rsync -av /mnt/Data/Video/ /mnt/Orico/Video`
`rsync -av /mnt/Data/DOC/ /mnt/Orico/DOC`
`rsync -av /mnt/Data/Курсы/ /mnt/Orico/Курсы`
`rsync -av /mnt/Data/Julia/ /mnt/Orico/Julia`
`rsync -av /mnt/Data/Julia/ /mnt/Orico/Julia