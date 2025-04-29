### CORE
## Resumen de Conexiones Core BGP

| Enlace       | Dispositivo | IP Address      | Vecino BGP      | AS Vecino | Red de Enlace    |
|--------------|-------------|-----------------|-----------------|-----------|------------------|
| ISP1 ↔ ISP2  | ISP1        | 172.39.0.33/30  | 172.39.0.34     | 65002     | 172.39.0.32/30   |
|              | ISP2        | 172.39.0.34/30  | 172.39.0.33     | 65001     |                  |
| ISP1 ↔ ISP3  | ISP1        | 172.39.0.37/30  | 172.39.0.38     | 65003     | 172.39.0.36/30   |
|              | ISP3        | 172.39.0.38/30  | 172.39.0.37     | 65001     |                  |
| ISP2 ↔ ISP3  | ISP2        | 172.39.0.41/30  | 172.39.0.42     | 65003     | 172.39.0.40/30   |
|              | ISP3        | 172.39.0.42/30  | 172.39.0.41     | 65002     |                  |

- **ISP1**: Telecom Uno (AS 65001)
- **ISP2**: Redes Nacionales (AS 65002)
- **ISP3**: Link Global (AS 65003)

ISP1 -> ISP2
ISP1 -> ISP3
ISP2 -> ISP1
ISP2 -> ISP3
ISP3 -> ISP1
ISP3 -> ISP2

### **ISP 1: Telecom Uno - Subneteo y VLANs**

#### **Red asignada:** `192.168.13.0/24`

| Departamento       | Subred            | Máscara           | Rango Utilizable                | Gateway        | Broadcast      | Hosts |
|--------------------|-------------------|-------------------|---------------------------------|----------------|----------------|-------|
| ADMINISTRACION_1   | 192.168.13.0      | 255.255.255.192   | 192.168.13.1 - 192.168.13.62    | 192.168.13.1   | 192.168.13.63  | 62    |
| ADMINISTRACION_2   | 192.168.13.64      | 255.255.255.192  | 192.168.13.65 - 192.168.13.126  | 192.168.13.65  | 192.168.13.127 | 62    |
| ATENCION_CLIENTE_1 | 192.168.13.128    | 255.255.255.224   | 192.168.13.129 - 192.168.13.158 | 192.168.13.129 | 192.168.13.159 | 30    |
| ATENCION_CLIENTE_2 | 192.168.13.160    | 255.255.255.224   | 192.168.13.161 - 192.168.13.190 | 192.168.13.161 | 192.168.13.191 | 30    |
| DHCP_SERVER        | 192.168.13.192    | 255.255.255.224   | 192.168.13.193 - 192.168.13.222 | 192.168.13.193 | 192.168.13.223 | 30    |


#### **Asignación de VLANs**

| VLAN ID | Nombre                 |
|---------|------------------------|
| 13      | ADMINISTRACION_1       |
| 14      | ADMINISTRACION_2       |
| 23      | ATENCION_CLIENTE_1     |
| 24      | ATENCION_CLIENTE_2     |
| 103     | DHCP_SERVER            |



ISP1 -> R0
R0 -> R1 -> S1 -> PC1 (DE R0 A R1 hay lacp)
R0 -> R2 -> S2 -> PC2
R0 -> R3 -> S3 -> PC3
R0 -> R4 -> S4 -> PC4 (DE R0 A R4 hay lacp)
R0 -> S0 -> Servidor DCHP

### **ISP 2: Redes Nacionales **

#### **Red asignada:** `192.168.23.0/24`

| VLAN ID | Nombre          | Subred            | Máscara            | Rango Utilizable         | Gateway          | Hosts |
|---------|-----------------|-------------------|--------------------|--------------------------|------------------|-------|
| 43      | VENTAS          | 192.168.23.0/26   | 255.255.255.192    | 192.168.23.1-62          | 192.168.23.1     | 62    |
| 43      | VENTAS          | 192.168.23.64/26   | 255.255.255.192   | 192.168.23.65-126        | 192.168.23.65    | 62    |
| 33      | FACTURACION     | 192.168.23.128/25 | 255.255.255.128    | 192.168.23.129-254       | 192.168.23.129   | 126   |

#### **Configuración DHCP Mejorada**

| Pool DHCP        | Subred            | Gateway         | DNS Server   |
|------------------|-------------------|-----------------|--------------|
| Ventas1_Pool     | 192.168.23.0/26   | 192.168.23.1    | |
| Ventas2_Pool     | 192.168.23.64/26  | 192.168.23.65   | |
| Facturacion_Pool | 192.168.23.128/25 | 192.168.23.129  | |

network 172.39.0.20 0.0.0.3 
network 172.39.0.24 0.0.0.3


ISP2 -> 2R1
2R1 -> 2R2 (LACP)
2R2 -> Wireless1 -> Dispositivos finales
2R2 -> Wireless2 -> Dispositivos finales
2R1 -> 2R3 (LACP)
2R3 -> 2S1 -> Dispositivos finales
2R3 -> 2S2 -> Dispositivos finales


# ISP 3: Conexiones Futuras - Subneteo Simplificado

### Tabla de Subredes

| Departamento | Subred            | Máscara           | Rango Utilizable         | Gateway        | VLAN ID | Hosts  |
|--------------|-------------------|-------------------|--------------------------|----------------|---------|--------|
| SOPORTE      | 192.168.33.0/25   | 255.255.255.128   | 192.168.33.1 - 126       | 192.168.33.1   | 53      | 126    |
| SEGURIDAD    | 192.168.33.128/26 | 255.255.255.192   | 192.168.33.129 - 190     | 192.168.33.129 | 63      | 62     |
| HTTP_SERVER  | 192.168.33.192/28 | 255.255.255.240   | 192.168.33.193 - 206     | 192.168.33.193 | 83      | 14     |
| DNS_SERVER   | 192.168.33.208/28 | 255.255.255.240   | 192.168.33.209 - 222     | 192.168.33.209 | 93      | 14     |

---

### Configuración Básica en Packet Tracer

#### **Asignación de VLANs**

| VLAN ID | Nombre         |
|---------|----------------|
| 53      | SOPORTE        |
| 63      | SEGURIDAD      |
| 83      | HTTP_SERVER    |
| 93      | DNS_SERVER     |


#isp 3

ISP3(sw de capa 3 del core ) -> 3R2
3R2 -> 3R0  (HSRP)
3R2 -> 3R1  (HSRP)
3R1 -> 3S5 -> Servidores htpp y dns
3R2 -> 3S5 -> Servidores htpp y dns
3R2 -> 3R4 (LACP)
3R2 -> 3R5 (LACP)
3R3 -> 3S3 -> Dispositivos finales
3R4 -> 3S4 -> Dispositivos finales

