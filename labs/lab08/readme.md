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

Адресация и линки были настроены ранее. Поэтому исходные данные - минимально настроенные линки, VLAN, HSRP.



Единственное, что следует еще настроить HSRP приоритет - ввиду более высокого адреса основным маршрутизатором будет R16. Сделаем основным R17, чтобы снизить нагрузку на R16, обеспечиающему связь до R32.

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

Выполняем базовую настройку EIGRP на маршрутизаторах СПб.



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
no shutdown
exit 








address-family ipv6 unicast autonomous-system 1
eigrp router-id 1.0.0.16
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
no shutdown
exit 




address-family ipv6 unicast autonomous-system 1
eigrp router-id 1.0.0.17
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
