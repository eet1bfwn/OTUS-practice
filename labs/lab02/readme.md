

# Развертывание коммутируемой сети с резервными каналами

Исходные данные

Задачи

Результирующие конфигурации устройств



## Исходные данные

### Топология

![](screenshots/2021-03-11-19-46-28-image.png)

### Таблица адресации

| Устройство | Интерфейс | IP-адрес    | Маска подсети |
| ---------- | --------- | ----------- | ------------- |
| S1         | VLAN 1    | 192.168.1.1 | 255.255.255.0 |
| S2         | VLAN 1    | 192.168.1.2 | 255.255.255.0 |
| S3         | VLAN 1    | 192.168.1.3 | 255.255.255.0 |

## Задачи

### Часть 1: Создание сети и настройка основных параметров устройства

В части 1 вам предстоит настроить топологию
сети и основные параметры маршрутизаторов.

**Шаг 1: Создайте сеть согласно топологии.**



Подключите устройства, как показано в топологии,
и подсоедините необходимые кабели.

**Шаг 2: Выполните инициализацию и перезагрузку
коммутаторов.**

Шаг 3: Настройте базовые параметры
каждого коммутатора.

a. Отключите поиск DNS.

b. Присвойте имена устройствам в соответствии
с топологией.

c. Назначьте **class** в качестве зашифрованного пароля доступа к привилегированному
режиму.

d. Назначьте **cisco** в качестве паролей консоли и VTY и активируйте
вход для консоли и VTY каналов.

e. Настройте logging synchronous для
консольного канала.

f. Настройте баннерное сообщение дня
(MOTD) для предупреждения пользователей о запрете несанкционированного
доступа.

g. Задайте IP-адрес, указанный в таблице
адресации для VLAN 1 на всех коммутаторах.

h. Скопируйте текущую конфигурацию в файл
загрузочной конфигурации.

```
enable
configure terminal
no ip domain-lookup
hostname S1
enable secret class
line console 0
logging synchronous
password cisco
login
line tty 0 15
password cisco
login
banner motd #Authorized access only!#
interface vlan 1
ip address 192.168.1.1 255.255.255.0
no shutdown
```



```
enable
configure terminal
no ip domain-lookup
hostname S2
enable secret class
line console 0
logging synchronous
password cisco
login
line tty 0 15
password cisco
login
banner motd #Authorized access only!#
interface vlan 1
ip address 192.168.1.2 255.255.255.0
no shutdown
```



```
enable
configure terminal
no ip domain-lookup
hostname S3
enable secret class
line console 0
logging synchronous
password cisco
login
line tty 0 15
password cisco
login
banner motd #Authorized access only!#
interface vlan 1
ip address 192.168.1.3 255.255.255.0
no shutdown
```

Summary:Шаг 4: Проверьте связь.

Проверьте способность компьютеров
обмениваться эхо-запросами.
