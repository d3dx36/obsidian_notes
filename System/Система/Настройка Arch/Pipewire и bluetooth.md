z`## 1. Установите необходимые утилиты

bash

# Установите usbutils для команды lsusb
sudo pacman -S usbutils

# Установите bluez-utils для работы с Bluetooth
sudo pacman -S bluez-utils

## 2. Проверьте наличие Bluetooth адаптера

bash

# Теперь проверьте USB устройства
lsusb

# Или более специфично
lsusb | grep -i blue

# Проверьте PCI устройства
lspci | grep -i blue

## 3. Проверьте модули ядра

bash

# Проверьте загруженные модули
lsmod | grep -i bt
lsmod | grep -i blue

# Проверьте доступные модули
modprobe -c | grep bt

## 4. Проверьте информацию о Bluetooth

bash

# Используйте bluetoothctl для проверки
bluetoothctl list

# Или
bluetoothctl show

# Проверьте доступные контроллеры
hciconfig -a

## 5. Проверьте dmesg с правами root

bash

# Проверьте сообщения ядра о Bluetooth
sudo dmesg | grep -i blue
sudo dmesg | grep -i bt

## 6. Проверьте аппаратное наличие

bash

# Проверьте наличие Bluetooth интерфейсов
ls /sys/class/bluetooth/ 2>/dev/null || echo "Нет Bluetooth интерфейсов"

# Проверьте RFKILL статус
sudo rfkill list

## 7. Перезагрузите службу

bash

# Полностью перезагрузите Bluetooth
sudo systemctl restart bluetooth.service

# Проверьте статус после перезагрузки
sudo systemctl status bluetooth.service

## 8. Если адаптер не обнаружен

Если после всех проверок адаптер не обнаружен, возможны следующие причины:

1. **Адаптер физически отсутствует** - проверьте, есть ли у вас Bluetooth адаптер
    
2. **Адаптер отключен в BIOS/UEFI** - проверьте настройки BIOS
    
3. **Проблема с драйверами** - может потребоваться установка специфичных драйверов
    
4. **Адаптер не поддерживается ядром** - проверьте модель адаптера
    

## 9. Проверьте после перезагрузки

bash

# Перезагрузите систему
sudo reboot

# После перезагрузки проверьте
sudo systemctl status bluetooth.service
bluetoothctl list`