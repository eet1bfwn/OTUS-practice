IBGP
Цель:

Настроить iBGP в офисе Москва Настроить iBGP в сети провайдера Триада Организовать полную IP связанность всех сетей

В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

    iBGP в офисом Москва между маршрутизаторами R14 и R15
    Настроите iBGP в провайдере Триада
    Настройте офиса Москва так, чтобы приоритетным провайдером стал Ламас.
    Настройте офиса С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно
    Все сети в лабораторной работе должны иметь IP связность
    


Перед выполнением задания корректируем настройки, выполненные ранее.

1. Смотрим R14-15 
2. sh ip bgp summary - состояния с соседом по зоне - Idle/Active.
3. sdebug bgp ipv4 unicast in - видим, что сосед refused, причем сосед странный, мы с ним не должны пытаться устанавливать соседство.
4. sh run | sec bgp - так и есть, мы неправильно указали соседа. Исправляем. 
5. Связность появляется.

Далее - как известно, внешние сети не рекомендуется добавлять в IGP. ??? кстати, почему?

Поэтому мы убираем внешние сети во всех настроенных зонах.


Москва (OSPF).
R14

en
conf t
interface Ethernet0/2
 no ip ospf 1 area 0
 no ipv6 ospf 1 area 0
 

router ospfv3 1
 passive-interface Ethernet0/2
!
router ospf 1
  passive-interface Ethernet0/2
end
wr




!!! ктати, ранее мы забыли указать для ipv6 пассивные интерфейсы.







R15

en
conf t
interface Ethernet0/2
 no ip ospf 1 area 0
 no ipv6 ospf 1 area 0
 

router ospfv3 1
 passive-interface Ethernet0/2
!
router ospf 1
  passive-interface Ethernet0/2
end
wr




СПб R18

en
conf t
router eigrp NG
 !
 address-family ipv4 unicast autonomous-system 1
  !
  no network 10.255.255.28 0.0.0.3
  no network 10.255.255.32 0.0.0.3
end
wr


причем для ipv6 таких настроек с включением на интерфейсах не задавалось, соответственно и команды на отключение нужны ли другие? Нужно гасить прямо на интерфейсах. Неудобно как-то

en
conf t
int range e0/2-3
no ipv6 eigrp 1
end
wr

не помогло, команда в конфиг не провалилась.


en
conf t

router eigrp NG
 address-family ipv6 unicast autonomous-system 1
 af-interface E0/2
 shutdown
 af-interface E0/3
 shutdown
 end
 wr
 
 
 Вот так получилось
 


