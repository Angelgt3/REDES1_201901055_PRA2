# Manual Técnico

## Tabla Resumen
| Dispositivo  | Interfaz |      IP     | Máscara de subred |
|--------------|----------|-------------|-------------------|
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

## 1. Configuración de Routers R1, R2 y R6, Switches SW1 y SW3, VPC11 y VPC14

### Configuración de Routers

#### **Router R1**
```
enable  
configure terminal  
hostname R1  
interface FastEthernet0/0  
ip address [IP_R1_F0/0] [Máscara_R1_F0/0]  
no shutdown  
exit  
interface FastEthernet0/1  
ip address [IP_R1_F0/1] [Máscara_R1_F0/1]  
no shutdown  
exit  
ip route [Red_Destino] [Máscara] [Next_Hop]  
exit  
```
#### **Router R2**
```
enable  
configure terminal  
hostname R2  
interface FastEthernet0/0  
ip address [IP_R2_F0/0] [Máscara_R2_F0/0]  
no shutdown  
exit  
interface FastEthernet0/1  
ip address [IP_R2_F0/1] [Máscara_R2_F0/1]  
no shutdown  
exit  
ip route [Red_Destino] [Máscara] [Next_Hop]  
exit  
```

#### **Router R6**
```
enable  
configure terminal  
hostname R6  
interface FastEthernet0/0  
ip address [IP_R6_F0/0] [Máscara_R6_F0/0]  
no shutdown  
exit  
interface FastEthernet0/1  
ip address [IP_R6_F0/1] [Máscara_R6_F0/1]  
no shutdown  
exit  
ip route [Red_Destino] [Máscara] [Next_Hop]  
exit  
```
### Configuración de Switches

#### **Switch SW1**
```
enable  
configure terminal  
hostname SW1  
interface range FastEthernet0/1 - 24  
channel-group 1 mode desirable  
exit  
```
#### **Switch SW3**
```
enable  
configure terminal  
hostname SW3  
interface range FastEthernet0/1 - 24  
channel-group 1 mode active  
exit  
```
### Configuración de VPCs

#### **VPC11**
```
ip 192.168.0.11 255.255.255.0 192.168.0.1  
```
#### **VPC14**
```
ip 192.168.0.14 255.255.255.0 192.168.0.1  
```

## 2. Resumen de los Comandos Usados

### **Creación de Rutas Estáticas**

```
ip route [Red_Destino] [Máscara] [Next_Hop]
```
- **[IP_R1_F0/0], [IP_R2_F0/0], [IP_R6_F0/0]**: Son las direcciones IP que asignas a las interfaces de cada router según la tabla de configuración.

  Ejemplo en el Router R1:
  ip address 152.168.1.2 255.255.255.248

- **[Máscara]**: Es la máscara de subred que determina cuántos bits corresponden a la red y cuántos a los hosts en la IP.

  Ejemplo:
  ip address 152.168.1.2 255.255.255.248

- **[Red_Destino]**: Es la red a la que quieres llegar utilizando una ruta estática.

  Ejemplo para llegar desde R1 a la red de R2:
  ip route 152.168.0.0 255.255.255.0 152.168.1.1

- **[Next_Hop]**: Es la dirección IP del router adyacente, el cual es el siguiente salto al que se envían los paquetes para alcanzar la red destino.


### **Creación de PortChannel con PAGP y LACP**

#### **PAGP** (Desirable Mode)
```
interface range FastEthernet0/1 - 24  
channel-group 1 mode desirable  
```
#### **LACP** (Active Mode)
```
interface range FastEthernet0/1 - 24  
channel-group 1 mode active  
```
### **Creación de IP Virtual con HSRP**
```
interface FastEthernet0/0  
standby 1 ip [IP_Virtual]  
standby 1 priority 110  
standby 1 preempt  
exit  
```
### **Configuración de VPCs**
```
ip [Dirección_IP] [Máscara] [Gateway]  
```

## 3. Comandos para la Verificación del Funcionamiento

### **Verificación de Rutas Estáticas**
```
show ip route  
```
### **Verificación de HSRP**
```
show standby brief  
```
### **Verificación de PortChannel**
```
show etherchannel summary  
```
### **Verificación de Conectividad (Ping)**
```
ping [Dirección_IP]  
```