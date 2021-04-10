# Маршрутизация на основе политик (PBR)

PBR

Цель:

Настроить политику маршрутизации в офисе Чокурдах
Распределить трафик между 2 линками

В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

1. Настроите политику маршрутизации для сетей офиса
2. Распределите трафик между двумя линками с провайдером
3. Настроите отслеживание линка через технологию IP SLA
4. Настройте для офиса Лабытнанги маршрут по-умолчанию
5. План работы и изменения зафиксированы в документации

Документация оформлена на github. (желательно использовать markdown)

Если нужна помощь - пишите через ЛК с помощью кнопки "чат с преподавателем" или в канал в Slack

Критерии оценки:

Статус "Принято" ставится при выполнении всех перечисленных требований к заданию.

# Базовая настройка сетевых устройств

Схема участка сети, необходимого для выполнения задания:
![](screenshots/2021-04-10-21-29-56-image.png)

Настраиваем SW29 - адресация, VLAN, Trunking:

```
!
hostname SW29
!
vtp mode off
!
!
vlan 8
 name Native
!
vlan 30
 name VPC30
!
vlan 31
 name VPC31
!
vlan 40
 name Management
!
vlan 90
 name ParkingLot
!
interface Ethernet0/0
 switchport access vlan 30
 switchport trunk encapsulation dot1q
 switchport mode access
!
interface Ethernet0/1
 switchport access vlan 31
 switchport trunk encapsulation dot1q
 switchport mode access
!
interface Ethernet0/2
 switchport trunk allowed vlan 30,31,40
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 8
 switchport mode trunk
!
interface Ethernet0/3
 switchport access vlan 90
 switchport trunk encapsulation dot1q
 switchport mode access
 shutdown
!
interface Vlan40
 ip address 10.14.40.49 255.255.255.0
 ipv6 address 2001:DB8:14:40::49/64
!
ip default-gateway 10.14.40.1
```

Настраиваем R28 - адресация, включение ipv6, dhcp:

```
hostname R28
!
!
ip dhcp excluded-address 10.14.40.1
ip dhcp excluded-address 10.14.30.1
ip dhcp excluded-address 10.14.31.1
ip dhcp excluded-address 10.14.40.1 10.14.40.100
ip dhcp excluded-address 10.14.30.1 10.14.30.100
ip dhcp excluded-address 10.14.31.1 10.14.31.100
!
ip dhcp pool R28-VLAN30
 network 10.14.30.0 255.255.255.0
 default-router 10.14.30.1
!
ip dhcp pool R28-VLAN31
 network 10.14.31.0 255.255.255.0
 default-router 10.14.31.1
!
ipv6 unicast-routing
!
interface Ethernet0/0
 description ISP_0
 ip address 10.255.255.26 255.255.255.252
 ipv6 address FE80::2 link-local
 ipv6 address 2001:DB8:255:255:24::26/80
!
interface Ethernet0/1
 description ISP_1
 ip address 10.255.255.22 255.255.255.252
 ipv6 address FE80::2 link-local
 ipv6 address 2001:DB8:255:255:20::22/80
!
interface Ethernet0/2
 description LAN
 no ip address
!
interface Ethernet0/2.30
 description LAN_VLAN30
 encapsulation dot1Q 30
 ip address 10.14.30.1 255.255.255.0
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:14:30::1/64
!
interface Ethernet0/2.31
 description LAN_VLAN31
 encapsulation dot1Q 31
 ip address 10.14.31.1 255.255.255.0
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:14:31::1/64
!
interface Ethernet0/2.40
 description LAN_Management
 encapsulation dot1Q 40
 ip address 10.14.40.1 255.255.255.0
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:14:40::1/64
!
```

Настраиваем R25 - адресация, включение ipv6:

```
hostname R25
!
ipv6 unicast-routing
ipv6 cef
!
interface Ethernet0/0
 ip address 10.52.255.2 255.255.255.252
 ipv6 address FE80::2 link-local
 ipv6 address 2001:DB8:52:255::2/80
!
interface Ethernet0/1
 ip address 10.255.255.5 255.255.255.252
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:255:255:4::5/80
!
interface Ethernet0/2
 ip address 10.52.255.13 255.255.255.252
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:52:255:12::13/80
!
interface Ethernet0/3
 ip address 10.255.255.21 255.255.255.252
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:255:255:20::21/80
!
```

Настраиваем R26 - адресация, включение ipv6:

```
hostname R26
!
ipv6 unicast-routing
!
interface Ethernet0/0
 ip address 10.52.255.6 255.255.255.252
 ipv6 address FE80::2 link-local
 ipv6 address 2001:DB8:52:255:4::6/80
!
interface Ethernet0/1
 ip address 10.255.255.25 255.255.255.252
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:255:255:24::25/80
!
interface Ethernet0/2
 ip address 10.52.255.14 255.255.255.252
 ipv6 address FE80::2 link-local
 ipv6 address 2001:DB8:52:255:12::14/80
!
interface Ethernet0/3
 ip address 10.255.255.33 255.255.255.252
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:255:255:32::33/80
!
```

После данных настроек между маршрутизаторами R25, R26, R28 есть прямая связность. VPC30 связи с R25 и R26 не имеет, т.к. на них нет маршрута до подсети, в которой находится компьютер.

!!! Но при использовании ipv6 связь есть !!! Это глюк? 

## Настройка NAT на R28

**Создаем ACL для выделения нужного трафика** ???Правильная ли терминология???

```
ip access-list standard NAT
permit 10.14.0.0 0.0.255.255
exit
```

???Правильно ли, что создаем максимально широкий ACL??? Или стоит под каждую подсеть сделать свой???

При настройке NAT через двух провайдеров с помощью команды:

```
ip nat inside source list NAT interface e0/1
ip nat inside source list NAT interface e0/0 
```

В конфигурации остается только одна запись - последняя. Поэтому использование ACL для выхода в интернет через двух провайдеров не подходит. ???Это особенность виртуализации???

Убираем настройку:

```
no ip nat inside source list NAT interface e0/0

no ip nat inside source list NAT interface e0/1
```

Создаем route-map:

```
route-map NAT_ISP0 permit 10
match ip address NAT
exit

route-map NAT_ISP1 permit 10
match ip address NAT
exit
```

Задаем настройку трансляции:

```
ip nat inside source route-map NAT_ISP0 interface e0/0 overload
ip nat inside source route-map NAT_ISP1 interface e0/1 overload
```

Отмечаем интерфейсы как inside:

```
int range e0/2.30 - ethernet 0/2.40
ip nat inside
no int range e0/2.32 - ethernet 0/2.39
```

Отмечаем интерфейсы как outside:

```
int range e0/0 - 1
ip nat outside
```

Выполняем проверку связи с R25:
![](screenshots/2021-04-10-22-16-30-image.png)

Таймаут. По таблице трансляции видим, что идет трансляция на адрес интерфейса маршрутизатора, который не подключен к цели. Соответственно, трансляция происходит неправильно.

---

Выполняем проверку связи с R26:
![](screenshots/2021-04-10-22-17-38-image.png)

Связь есть, трансляция происходит правильно. Вывод - трансляция идет по первому правилу в конфиге - ip nat inside source route-map NAT_ISP0 interface Ethernet0/0 overload - через интерфейс E0/0.

Указываем в route-map дополнительную строку с выходным интерфейсом:

```
route-map NAT_ISP0 permit 10
match ip address NAT
match interface e0/0
exit

route-map NAT_ISP1 permit 10
match ip address NAT
match interface e0/1
exit

```

Работа через двух провайдеров минимально настроена. Связь есть:
![](screenshots/2021-04-10-22-26-40-image.png)

???В настройках route-map должен быть обязательно указан адрес выходного интерфейса, чтобы трансляция отрабатывала верно. Что значит эта строка???


