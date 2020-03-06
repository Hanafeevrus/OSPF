# OSPF    
[OSPF](https://ru.bmstu.wiki/OSPF_(Open_Shortest_Path_First)) (англ. Open Shortest Path First) — протокол динамической маршрутизации, основанный на технологии отслеживания    
состояния канала (link-state technology) и использующий для нахождения кратчайшего пути алгоритм Дейкстры.    
#### Задача   
- Поднять три виртуалки
- Объединить их разными private network
1. Поднять OSPF между машинами средствами программного маршрутизатора Quagga.
2. Изобразить ассиметричный роутинг
3. Сделать один из линков "дорогим", но что бы при этом роутинг был симметричным    
#### Описание   
Quagga-пакет свободного программного обеспечения, поддерживающий протоколы динамической маршрутизации IP. [Подробно](https://www.nongnu.org/quagga/docs/quagga.html) по настройкам синтаксису и прочему Quagga.   
Quagga на текущий момент заменен проектом [FRRouting](https://frrouting.org/)
#### Реализация   
1. `Vagrant up` поднимает три машины обьедененные private сетями и настроенным программным маршрутизатором Quagga.    
                    ![Routing](https://github.com/Hanafeevrus/OSPF/blob/master/OSPF%20Diagram.jpg)    
                      
Основной конфигурационный файл OSPF - `/etc/quagga/ospfd.conf`  
Конфигурационный файл роутера r1:   
```   
hostname ospfd
password zebra
log stdout
log file /var/log/quagga/ospf.log
interface eth1
ip ospf mtu-ignore
ip ospf network point-to-point
ip ospf hello-interval 5
ip ospf dead-interval 10
interface eth2
ip ospf mtu-ignore
ip ospf network point-to-point
ip ospf hello-interval 5
ip ospf dead-interval 10
router ospf
network 192.168.10.0/24 area 12
network 172.16.15.0/24 area 12
neighbor 192.168.10.12
neighbor 172.16.15.13
default-information originate always    
```   
Соседние маршрутизаторы настраиваются аналогично, меняя network и neighbor.   
Утилита vtysh позволяет управлять и просматривать некоторые параметры.    
Например просмотреть соседние относительно интерйфейсов маршрутизаторы.   
```
r1# sh ip ospf  neighbor  

    Neighbor ID Pri State           Dead Time Address         Interface            RXmtL RqstL DBsmL
10.11.12.12       1 Full/DROther       7.111s 192.168.10.12   eth1:192.168.10.10       0     0     0
10.11.12.13       1 Full/DROther       9.570s 172.16.15.13    eth2:172.16.15.10        0     0     0    

r2# sh ip ospf  neighbor  

    Neighbor ID Pri State           Dead Time Address         Interface            RXmtL RqstL DBsmL
10.0.2.15         1 Full/DROther       8.380s 192.168.10.10   eth1:192.168.10.12       0     0     0
10.11.12.13       1 Full/DROther       8.274s 10.11.12.13     eth2:10.11.12.12         0     0     0

r3# sh ip ospf  neighbor  

    Neighbor ID Pri State           Dead Time Address         Interface            RXmtL RqstL DBsmL
10.11.12.12       1 Full/DROther       8.135s 10.11.12.12     eth1:10.11.12.13         0     0     0
10.0.2.15         1 Full/DROther       5.705s 172.16.15.10    eth2:172.16.15.13        0     0     0    
```   
2. Чтобы роутинг был ассиметричным в данном случае необходима метрика- стоимость, присвоенная непосредственному каналу от данного маршрутизатора к данной сети, в моем случае это R1 eth2, анонсирующий сеть 172.16.15.0/24   
`ansible-playbook -i inventories/hosts playbooks/r1-arouting.yml` пропишет "стоимость" на интерфейсе eth2. Чем меньше стоимость тем предпочтительней маршрут.   
3. Сделать один линк "дорогим".   
Необходимо на обеих маршрутизаторах, анонсирующих линк, выставить равную стоимость. В моем случае "дорогим" выбран линк `172.16.15.0/24`, следовательно на интерфейсе роутера `R3`
необходимо выставить стоимость равную `R1 eth2`.     
`ansible-playbook -i inventories/hosts playbooks/r3-routing.yml`    
Условия из 2 пункта должны быть выполненны.   
`ansible-playbook -i inventories/hosts playbooks/r1-arouting.yml`
