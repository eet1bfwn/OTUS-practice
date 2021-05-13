# EIGRP

Цель:

Настроить EIGRP в С.-Петербург;
Использовать named EIGRP

1. В офисе С.-Петербург настроить EIGRP
2. R32 получает только маршрут по-умолчанию
3. R16-17 анонсируют только суммарные префиксы
4. Использовать EIGRP named-mode для настройки сети

Настройка осуществляется одновременно для IPv4 и IPv6

## Схема

![](screenshots/2021-05-12-22-37-26-image.png)

## Базовая настройка устройств

Маршрутизаторы R16-17 резервируют друг друга, являясь шлюзом по умолчанию для клиентов, по протоколу HSPR.

В Москве маршрутизаторы R12 и R13 были заменены на L3-коммутаторы, т.к. требовалось 

Адресация и линки были настроены ранее. Поэтому исходные данные - минимально настроенные линки в соответствии со схемой, VLAN (10, 40, 80), HSRP.

Порты e0/0,2 на маршрутизаторах объединены в бридж. Это позволяет использовать резер

Единственное, что следует еще настроить HSRP-приоритет - ввиду более высокого адреса основным маршрутизатором будет R16. Сделаем основным R17, чтобы снизить нагрузку на R16, обеспечиающему связь до R32.

R17:

```
en

conf t

int bvi 40

standby priority 255

standby preempt

exit

int bvi 10

standby priority 255

standby preempt

exit

int bvi 80

standby priority 255

standby preempt

exit

end 

wr
```

## Настраиваем EIGRP

Выполняем базовую настройку EIGRP на маршрутизаторах СПб. Используем named-mode. Заходим в конфигурацию протокола, задаем автономную систему, router-id, включаем процесс. Для ipv4 указываем также интерфейсы, которые будут участвовать. На маршрутизаторах R16-17 настраиваем интерфейсы, смотрящие в сторону конечных клиентов (BVI10, BVI80)

R16:

```
en
conf t
router eigrp NG
address-family ipv4 unicast autonomous-system 1
network 10.78.40.0 0.0.0.255
network 10.78.10.0 0.0.0.255
network 10.78.80.0 0.0.0.255
network 10.78.255.8 0.0.0.3
network 10.78.255.4 0.0.0.3
eigrp router-id 1.0.0.16

af-interface BVI 10
passive-interface
exit
af-interface BVI 80
passive-interface
exit

no shutdown
exit 




address-family ipv6 unicast autonomous-system 1
eigrp router-id 1.0.0.16
af-interface BVI 10
passive-interface
exit
af-interface BVI 80
passive-interface
no shutdown


exit

end

wr
```

R17:

```
en
conf t
router eigrp NG
address-family ipv4 unicast autonomous-system 1
network 10.78.40.0 0.0.0.255
network 10.78.10.0 0.0.0.255
network 10.78.80.0 0.0.0.255
network 10.78.255.0 0.0.0.3
eigrp router-id 1.0.0.17

af-interface BVI 10
passive-interface
exit
af-interface BVI 80
passive-interface
exit
no shutdown
exit 




address-family ipv6 unicast autonomous-system 1
eigrp router-id 1.0.0.17
af-interface BVI 10
passive-interface
exit
af-interface BVI 80
passive-interface
no shutdown


exit

end

wr
```

R18:

```
en
conf t
router eigrp NG
address-family ipv4 unicast autonomous-system 1
network 10.78.255.0 0.0.0.3
network 10.78.255.4 0.0.0.3
network 10.255.255.28 0.0.0.3
network 10.255.255.32 0.0.0.3
eigrp router-id 1.0.0.18
no shutdown
exit 




address-family ipv6 unicast autonomous-system 1
eigrp router-id 1.0.0.18
no shutdown


exit

end

wr
```

R32:

```
en
conf t
router eigrp NG
address-family ipv4 unicast autonomous-system 1
network 10.78.255.8 0.0.0.3
eigrp router-id 1.0.0.32
no shutdown
exit 




address-family ipv6 unicast autonomous-system 1
eigrp router-id 1.0.0.32
no shutdown


exit

end

wr
```
