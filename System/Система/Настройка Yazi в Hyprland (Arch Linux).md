````
# Настройка Yazi в Hyprland (Arch Linux)

Заметка о том, как заставить консольный файловый менеджер Yazi корректно открывать файлы, архивы, exe и bash-скрипты без расширения (например, файл `start`).

## 1. Системные ассоциации (Графика)
Для стандартных медиафайлов (фото, видео, PDF) используется утилита `handlr-regex` (из AUR), заменяющая стандартный `xdg-open`.

В `~/.bashrc` или `~/.zshrc` добавлен алиас для прозрачной интеграции:
```bash
alias xdg-open="handlr open"
````

## 2. Конфигурация Yazi (`~/.config/yazi/yazi.toml`)

Финальный рабочий конфиг, учитывающий строгие правила парсера Yazi (требует обязательного наличия ключа `url` или `mime`).

Правила упорядочены так, чтобы общие типы вроде `text/*` не перехватывали скрипты раньше времени.

Ini, TOML

```
[opener]
edit = [
	{ run = '${EDITOR:-vim} "$@"', block = true, for = "unix" },
]
play = [
	{ run = 'mpv "$@"', orphan = true, for = "unix" },
]
view = [
	{ run = 'imv "$@"', orphan = true, for = "unix" },
]
extract = [
	{ run = 'ouch decompress "$@"', block = true, for = "unix" }
]
run_sh = [
	{ run = 'bash "$@"', block = true, for = "unix" }
]
run_exe = [
	{ run = 'wine "$@"', block = true, for = "unix" }
]

[open]
rules = [
	# 1. Ловим конкретные файлы по маске пути (чтобы работал файл 'start' без расширения)
	{ url = "*/start", use = [ "run_sh", "edit" ] },
	{ url = "*.sh", use = [ "run_sh", "edit" ] },
	{ url = "*.exe", use = "run_exe" },

	# 2. Ловим по MIME-типам
	{ mime = "text/x-shellscript", use = [ "run_sh", "edit" ] },
	{ mime = "application/x-msdos-program", use = "run_exe" },
	
	# Архивы (требуется пакет ouch)
	{ mime = "application/zip", use = "extract" },
	{ mime = "application/x-tar", use = "extract" },
	{ mime = "application/x-7z-compressed", use = "extract" },
	{ mime = "application/x-rar", use = "extract" },

	# 3. Общие типы опускаем в самый низ
	{ mime = "text/*", use = "edit" },
	{ mime = "video/*", use = "play" },
	{ mime = "image/*", use = "view" },
]
```

## Поведение клавиш в Yazi:

- **Enter** на файле `start` или `.sh` — автоматический запуск скрипта через `bash`.
    
- **Клавиша `o`** (меню Open with) — открывает выбор: запустить скрипт или открыть его код для редактирования в `Vim`.
    

## Чек-лист, если что-то не открывается:

Если скрипт всё равно пытается открыться в Vim вместо запуска, файлу не хватает прав исполняемого. Лечится командой в терминале:

Bash

```
chmod +x имя_файла
```