# Ejemplo Clase 6

### VLANs

|ID|NOMBRE|ID RED|MASCARA|GATEWAY
|-|-|-|-|-|
|10|VLAN10|192.168.10.0|255.255.255.0|192.168.10.1|
|20|VLAN20|192.168.20.0|255.255.255.0|192.168.20.1|
|30|VLAN30|192.168.30.0|255.255.255.0|192.168.30.1|
|40|VLAN40|192.168.40.0|255.255.255.0|192.168.40.1|

### Enrutamiento

|ID RED|MASCARA|PRIMERA IP USABLE|ULTIMA IP USABLE
|-|-|-|-|
|10.0.10.0|255.255.255.252|10.0.10.1|10.0.10.2|
|10.0.20.0|255.255.255.252|10.0.20.1|10.0.20.2|
|10.0.30.0|255.255.255.252|10.0.30.1|10.0.30.2|
|10.0.40.0|255.255.255.252|10.0.40.1|10.0.40.2|
|10.0.70.0|255.255.255.252|10.0.70.1|10.0.70.2|
|10.0.80.0|255.255.255.252|10.0.80.1|10.0.80.2|

### Red Inalambrica

**SSID:** REDES2 \
**PSK:** REDES2S2025

|ID RED|MASCARA|GATEWAY|IP INTERNET|
|-|-|-|-|
|ROUTER||||
|10.0.50.0|255.255.255.252|10.0.50.1|10.0.50.2|
|DHCP||||
|192.168.60.0|255.255.255.0|192.168.60.1|192.168.60.10|

### HSRP
|ID RED|MASCARA|IP VIRTUAL|IP DER|IP IZQ|
|-|-|-|-|-|
|192.168.10.0|255.255.255.0|192.168.10.1|192.168.10.2|192.168.10.3|
|192.168.20.0|255.255.255.0|192.168.20.1|192.168.20.2|192.168.20.3|
|192.168.30.0|255.255.255.0|192.168.30.1|192.168.30.2|192.168.30.3|
|192.168.40.0|255.255.255.0|192.168.40.1|192.168.40.2|192.168.40.3|


# Configuración de Dispositivos

## Edificio 1

### SW0
```bash
enable
configure terminal
hostname SW0
vlan 10
name VLAN10
vlan 20
name VLAN20
exit
interface range fastEthernet 0/3-24
switchport mode trunk
switchport trunk allowed vlan all
exit
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
exit
interface fastEthernet 0/2
switchport mode access
switchport access vlan 20
exit
do write
```

### R0
```shell
enable
configure terminal
hostname R0
interface range gigabitEthernet 0/0-1
no shutdown
exit
!--- Configuracion Interfaces Virtuales
interface gigabitEthernet 0/0.10
encapsulation dot1Q 10
ip address 192.168.10.2 255.255.255.0
no shutdown
exit
interface gigabitEthernet 0/0.20
encapsulation dot1Q 20
ip address 192.168.20.2 255.255.255.0
no shutdown
exit
!--- Configuracion IP Virtual
interface gigabitEthernet 0/0.10
standby 10 ip 192.168.10.1
standby 10 priority 100
standby 10 preempt
exit
interface gigabitEthernet 0/0.20
standby 20 ip 192.168.20.1
standby 20 priority 110
standby 20 preempt
exit
!-- Configuracion Interfaces Fisicas
interface gigabitEthernet 0/1
ip address 10.0.10.1 255.255.255.252
no shutdown
exit
!-- Configuracion Enrutamiento
router eigrp 1
network 192.168.10.0 0.0.0.255
network 192.168.20.0 0.0.0.255
network 10.0.10.0 0.0.0.3
no auto-summary
exit
!-- Configuracion DHCP
interface gigabitEthernet 0/0.10
ip helper-address 192.168.30.20
exit
interface gigabitEthernet 0/0.20
ip helper-address 192.168.30.20
exit
do write
```

### R01
```shell
enable
configure terminal
hostname R01
interface range gigabitEthernet 0/0-1
no shutdown
exit
!--- Configuracion Interfaces Virtuales
interface gigabitEthernet 0/0.10
encapsulation dot1Q 10
ip address 192.168.10.3 255.255.255.0
no shutdown
exit
interface gigabitEthernet 0/0.20
encapsulation dot1Q 20
ip address 192.168.20.3 255.255.255.0
no shutdown
exit
!--- Configuracion IP Virtual
interface gigabitEthernet 0/0.10
standby 10 ip 192.168.10.1
standby 10 priority 110
standby 10 preempt
exit
interface gigabitEthernet 0/0.20
standby 20 ip 192.168.20.1
standby 20 priority 100
standby 20 preempt
exit
!-- Configuracion Interfaces Fisicas
interface gigabitEthernet 0/1
ip address 10.0.20.1 255.255.255.252
no shutdown
exit
!-- Configuracion Enrutamiento
router eigrp 1
network 192.168.10.0 0.0.0.255
network 192.168.20.0 0.0.0.255
network 10.0.20.0 0.0.0.3
no auto-summary
exit
!-- Configuracion DHCP
interface gigabitEthernet 0/0.10
ip helper-address 192.168.30.20
exit
interface gigabitEthernet 0/0.20
ip helper-address 192.168.30.20
exit
do write
```

### MSW0
```shell
enable
configure terminal
hostname MSW0
ip routing
interface range fastEthernet 0/1-24
no shutdown
exit
!-- Configuracion Interfaces Fisicas
interface fastEthernet 0/1
no switchport
ip address 10.0.20.2 255.255.255.252
no shutdown
exit
interface fastEthernet 0/2
no switchport
ip address 10.0.10.2 255.255.255.252
no shutdown
exit
!-- Configuracion Agregacion Enlaces
interface range fastEthernet 0/3-6
no switchport
channel-group 1 mode active
no shutdown
exit
interface port-channel 1
ip address 10.0.70.1 255.255.255.252
no shutdown
exit
!-- Configuracion Enrutamiento
router eigrp 1
network 10.0.10.0 0.0.0.3
network 10.0.20.0 0.0.0.3
network 10.0.70.0 0.0.0.3
no auto-summary
exit
do write
```


## Edificio 2

### SW1
```bash
enable
configure terminal
hostname SW1
vlan 30
name VLAN30
vlan 40
name VLAN40
exit
interface range fastEthernet 0/3-24
switchport mode trunk
switchport trunk allowed vlan all
exit
interface fastEthernet 0/1
switchport mode access
switchport access vlan 30
exit
interface fastEthernet 0/2
switchport mode access
switchport access vlan 40
exit
do write
```

### R1
```shell
enable
configure terminal
hostname R1
interface range gigabitEthernet 0/0-1
no shutdown
exit
!--- Configuracion Interfaces Virtuales
interface gigabitEthernet 0/0.30
encapsulation dot1Q 30
ip address 192.168.30.2 255.255.255.0
no shutdown
exit
interface gigabitEthernet 0/0.40
encapsulation dot1Q 40
ip address 192.168.40.2 255.255.255.0
no shutdown
exit
!--- Configuracion IP Virtual
interface gigabitEthernet 0/0.30
standby 30 ip 192.168.30.1
standby 30 priority 100
standby 30 preempt
exit
interface gigabitEthernet 0/0.40
standby 40 ip 192.168.40.1
standby 40 priority 110
standby 40 preempt
exit
!-- Configuracion Interfaces Fisicas
interface gigabitEthernet 0/1
ip address 10.0.30.1 255.255.255.252
no shutdown
exit
!-- Configuracion Enrutamiento
router eigrp 1
network 192.168.30.0 0.0.0.255
network 192.168.40.0 0.0.0.255
network 10.0.30.0 0.0.0.3
no auto-summary
exit
do write
```

### R11
```shell
enable
configure terminal
hostname R11
interface range gigabitEthernet 0/0-1
no shutdown
exit
!--- Configuracion Interfaces Virtuales
interface gigabitEthernet 0/0.30
encapsulation dot1Q 30
ip address 192.168.30.3 255.255.255.0
no shutdown
exit
interface gigabitEthernet 0/0.40
encapsulation dot1Q 40
ip address 192.168.40.3 255.255.255.0
no shutdown
exit
!--- Configuracion IP Virtual
interface gigabitEthernet 0/0.30
standby 30 ip 192.168.30.1
standby 30 priority 110
standby 30 preempt
exit
interface gigabitEthernet 0/0.40
standby 40 ip 192.168.40.1
standby 40 priority 100
standby 40 preempt
exit
!-- Configuracion Interfaces Fisicas
interface gigabitEthernet 0/1
ip address 10.0.40.1 255.255.255.252
no shutdown
exit
!-- Configuracion Enrutamiento
router eigrp 1
network 192.168.30.0 0.0.0.255
network 192.168.40.0 0.0.0.255
network 10.0.40.0 0.0.0.3
no auto-summary
exit
do write
```

### MSW1
```shell
enable
configure terminal
hostname MSW1
ip routing
interface range fastEthernet 0/1-24
no shutdown
exit
!-- Configuracion Interfaces Fisicas
interface fastEthernet 0/1
no switchport
ip address 10.0.40.2 255.255.255.252
no shutdown
exit
interface fastEthernet 0/2
no switchport
ip address 10.0.30.2 255.255.255.252
no shutdown
exit
!-- Configuracion Agregacion Enlaces
interface range fastEthernet 0/3-6
no switchport
channel-group 1 mode passive
no shutdown
exit
interface port-channel 1
ip address 10.0.70.2 255.255.255.252
no shutdown
exit
interface range fastEthernet 0/7-10
no switchport
channel-group 2 mode active
no shutdown
exit
interface port-channel 2
ip address 10.0.80.1 255.255.255.252
no shutdown
exit
!-- Configuracion Enrutamiento
router eigrp 1
network 10.0.30.0 0.0.0.3
network 10.0.40.0 0.0.0.3
network 10.0.70.0 0.0.0.3
network 10.0.80.0 0.0.0.3
no auto-summary
exit
do write
```

## Edificio 3

### MSW2
```shell
enable
configure terminal
hostname MSW2
ip routing
interface range fastEthernet 0/1-24
no shutdown
exit
!-- Configuracion Interfaces Fisicas
interface fastEthernet 0/5
no switchport
ip address 10.0.50.1 255.255.255.252
no shutdown
exit
!-- Configuracion Agregacion Enlaces
interface range fastEthernet 0/1-4
no switchport
channel-group 2 mode passive
no shutdown
exit
interface port-channel 2
ip address 10.0.80.2 255.255.255.252
no shutdown
exit
!-- Configuracion Enrutamiento
router eigrp 1
network 10.0.50.0 0.0.0.3
network 10.0.80.0 0.0.0.3
no auto-summary
exit
do write
```
