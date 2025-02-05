## Vlans

| No | Vlan | Id Red | Mascara de red | Puerta de enlace predeterminada |
|-|-|-|-|-|
| 10| ESTUDIANTES | 192.168.10.0 | /24 | 192.168.10.1 |
| 20| PRIMARIA | 192.168.20.0 | /24 | 192.168.20.1 |
| 30| DIVERSIFICADO | 192.168.30.0 | /24 | 192.168.30.1 |

## Tabla de IP's

| Equipo | Vlan | IP | Mascara de red |
|-|-|-|-|
| PC0 | ESTUDIANTES | 192.168.10.10 | /24 |
| PC1 | PRIMARIA | 192.168.20.10 | /24 |
| PC2 | DIVERSIFICADO | 192.168.30.10 | /24 |
| PC3 | ESTUDIANTES | 192.168.10.11 | /24 |
| PC4 | ESTUDIANTES | 192.168.10.12 | /24 |
| PC5 | PRIMARIA | 192.168.20.11 | /24 |
| PC6 | DIVERSIFICADO | 192.168.30.11 | /24 |

## SW2

#### Configuracion interfaces troncales
```
enable
configure terminal
hostname SW2
interface range fastEthernet 0/1-24
switchport mode trunk
switchport trunk allow vlan all
exit
```

#### Configuracion VTP
```
vtp version 2
vtp mode server
vtp domain redes
vtp password redes
```

#### Configuracion VLANs
```
vlan 10
name ESTUDIANTES
exit
vlan 20
name PRIMARIA
exit
vlan 30
name DIVERSIFICADO
exit
```

## SW0

#### Configuracion interfaces troncales
```
enable
configure terminal
hostname SW0
interface range fastEthernet 0/4-24
switchport mode trunk
switchport trunk allow vlan all
exit
```

#### Configuracion VTP
```
vtp version 2
vtp mode client
vtp domain redes
vtp password redes
```

#### Configuracion interfaces de acceso
```
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
exit
interface fastEthernet 0/2
switchport mode access
switchport access vlan 20
exit
interface fastEthernet 0/3
switchport mode access
switchport access vlan 30
exit
```

## SW1

#### Configuracion interfaces troncales
```
enable
configure terminal
hostname SW0
interface range fastEthernet 0/5-24
switchport mode trunk
switchport trunk allow vlan all
exit
```

#### Configuracion VTP
```
vtp version 2
vtp mode client
vtp domain redes
vtp password redes
```

#### Configuracion interfaces de acceso
```
interface range fastEthernet 0/1-2
switchport mode access
switchport access vlan 10
exit
interface fastEthernet 0/3
switchport mode access
switchport access vlan 20
exit
interface fastEthernet 0/4
switchport mode access
switchport access vlan 30
exit
```

## R1

#### Configuracion inicial
```
enable
configure terminal
hostname R1
interface gigabitEthernet 0/0
no shutdown
exit
```

#### Configuracion intervlan
```
interface gigabitEthernet 0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
no shutdown
exit
interface gigabitEthernet 0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
no shutdown
exit
interface gigabitEthernet 0/0.30
encapsulation dot1Q 30
ip address 192.168.30.1 255.255.255.0
no shutdown
exit
```