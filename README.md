1X2x# Manual Técnico

## Tabla Resumen
| Dispositivo  | Interfaz |      IP     | Máscara de subred 
|--------------|----------|-------------|-------------------
| R1           | s1/0     | 10.0.0.1    | /30               |
| R1           | f0/0     | 152.168.1.2 | /29               |
| R1           | f0/1     | 152.168.2.2 | /29               |
| R2           | f0/0     | 152.168.1.1 | /29               |
| R2           | f0/1     | 152.168.0.2 | /24               |
| R3           | f0/0     | 152.168.2.1 | /29               |
| R3           | f0/1     | 152.168.0.3 | /24               |
| R2-R3        | Virtual  | 152.168.0.1 | /24               |
| VPC11        | -        | 152.168.0.4 | /24               |
| VPC13        | -        | 152.168.0.5 | /24               |
| R4           | s1/0     | 10.0.0.2    | /30               |
| R4           | f0/0     | 152.178.1.1 | /29               |
| R4           | f0/1     | 152.178.2.1 | /29               |
| R5           | f0/0     | 152.178.1.2 | /29               |
| R5           | f0/1     | 152.178.0.2 | /24               |
| R6           | f0/0     | 152.178.2.2 | /29               |
| R6           | f0/1     | 152.178.0.3 | /24               |
| R5-R6        | Virtual  | 152.178.0.1 | /24               |
| VPC12        | -        | 152.178.0.4 | /24               |
| VPC14        | -        | 152.178.0.5 | /24               |

## Implementación de las topologías

**Topologia**           

![Topologia](imagenes/Topologia.jpg)


## Detalle de los comandos usados

### SERIAL
R1
enable
configure terminal
interface s0/0
ip address 10.0.0.1 255.255.255.252
no shutdown
exit

R4
enable
configure terminal
interface s0/0
ip address 10.0.0.1 255.255.255.252
no shutdown
exit


show ip interface brief


### Configurar PortChannel con PAGP en SW0:
enable
configure terminal
interface range fastethernet0/21 - 22
channel-protocol pagp
channel-group 1 mode desirable
no shutdown
exit

### Configurar PortChannel con PAGP en SW1
enable
configure terminal
interface range fastethernet0/21 - 22
channel-protocol pagp
channel-group 1 mode auto
no shutdown
exit

### Configurar PortChannel con LACP en SW2:
enable
configure terminal
interface range fastethernet0/23 - 24
channel-protocol lacp
channel-group 1 mode active
no shutdown
exit

### Configurar PortChannel con LACP en SW2:
enable
configure terminal
interface range fastethernet0/23 - 24
channel-protocol lacp
channel-group 1 mode passive
no shutdown
exit

### Verificación del PortChannel:
show etherchannel summary

show run interface po1

### Configuración de HSRP en R2, R3, R5 Y R6:
enable
configure terminal
interface f0/0
ip address 152.168.0.2 255.255.255.0
standby 1 ip 152.168.0.1
standby 1 priority 110
standby 1 preempt
no shutdown
exit


enable
configure terminal
interface f0/0
ip address 152.168.0.3 255.255.255.0
standby 1 ip 152.168.0.1
standby 1 priority 100
standby 1 preempt
no shutdown
exit

enable
configure terminal
interface f0/1
ip address 152.178.1.2 255.255.255.248
standby 1 ip 152.178.0.1
standby 1 priority 110
standby 1 preempt
no shutdown
exit

enable
configure terminal
interface f0/0
ip address 152.178.2.2 255.255.255.248
standby 1 ip 152.178.0.1
standby 1 priority 100
standby 1 preempt
no shutdown
exit


### Configurar las rutas estáticas necesarias para garantizar la comunicación en ambas vías entre VPC11, VPC13, VPC12 y VPC14.

R2
enable
configure terminal
ip route 152.178.0.0 255.255.255.0 152.168.0.1
exit

R3
enable
configure terminal
ip route 152.178.0.0 255.255.255.0 152.168.0.1
exit

R5
enable
configure terminal
ip route 152.168.0.0 255.255.255.0 152.178.0.1
exit

R6
enable
configure terminal
ip route 152.168.0.0 255.255.255.0 152.178.0.1
exit

enable
configure terminal
interface FastEthernet0/1
ip address 152.168.0.2 255.255.255.0
no shutdown
exit
