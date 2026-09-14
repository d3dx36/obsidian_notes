
**Цель:** Открыть публичный доступ к папкам Pictures, Music, Movies и Documents для пользователя `yuri` без пароля.

## 1. Установка и зависимости

Для стабильной работы в Arch важно, чтобы все компоненты были одной версии.

Bash

```
sudo pacman -Syu samba smbclient libwbclient
```

## 2. Конфигурация (`/etc/samba/smb.conf`)

Файл должен содержать глобальные настройки для гостевого входа и блоки для каждой папки.

> [!danger] Внимание Samba требует пустую строку в самом конце файла конфигурации.

Ini, TOML

```
[global]
   workgroup = WORKGROUP
   server string = Samba Server %v
   security = user
   map to guest = bad user
   dns proxy = no

[Pictures]
   path = /home/yuri/Pictures
   writable = yes
   guest ok = yes
   force user = yuri

[Music]
   path = /home/yuri/Music
   writable = yes
   guest ok = yes
   force user = yuri

[Movies]
   path = /home/yuri/Movies
   writable = yes
   guest ok = yes
   force user = yuri

[Documents]
   path = /home/yuri/Documents
   writable = yes
   guest ok = yes
   force user = yuri
```

## 3. Права доступа (Filesystem)

Чтобы гость мог зайти в расшаренную папку внутри `/home/yuri`, у него должны быть права на "проход" через родительскую директорию.

- **Разрешить вход в домашнюю папку:** `chmod o+x /home/yuri`
    
- **Разрешить чтение/запись в целевых папках:** `chmod -R 777 ~/Pictures ~/Music ~/Movies ~/Documents`
    

## 4. Управление сервисами

Samba состоит из двух демонов:

1. **smb** — сам протокол доступа к файлам.
    
2. **nmb** — отвечает за то, чтобы компьютер был виден в сетевом окружении.
    

Bash

```
# Проверка конфига на ошибки
testparm /etc/samba/smb.conf

# Запуск и добавление в автозагрузку
sudo systemctl enable --now smb nmb

# Перезапуск после правок
sudo systemctl restart smb nmb
```

## 5. Решение проблем

- **Библиотеки (Library mismatch):** Если `testparm` выдает ошибку `version NOT FOUND`, выполните полную проверку обновлений: `sudo pacman -Syu`.
    
- **Логи:** Если сервис не стартует, смотреть тут: `sudo journalctl -xeu smb.service`.
    
- **IP адрес:** Узнать свой адрес в сети: `ip addr show`.