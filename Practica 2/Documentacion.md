# Topologia 



# Vlan

Nombre             | Vlan
-------------------|---------------
ADMIN              |  13       
ESTUDIANTES        |  23    
WEB_SERVERS        |  33    
DHCP_SERVER        |  43    


## Tabla de VLANs (Edificio de Ingeniería)

| Nombre VLAN      | ID VLAN          | Subred            | Gateway         | Máscara            | Rango de IPs útiles           | Hosts |
|------------------|------------------|-------------------|-----------------|--------------------|-------------------------------|-------|
| ADMIN            | 13      | 192.168.39.0/28    | 192.168.39.1     | 255.255.255.240    | 192.168.39.2 - 192.168.39.14    | 14    |
| ESTUDIANTES      | 23      | 192.168.39.16/26   | 192.168.39.17    | 255.255.255.192    | 192.168.39.18 - 192.168.39.78   | 60    |
| WLAN_PISO2       | 53      | 192.168.39.80/26   | 192.168.39.81    | 255.255.255.192    | 192.168.39.82 - 192.168.39.142  | 60    |
| WLAN_PISO3       | 63      | 192.168.39.144/26  | 192.168.39.145   | 255.255.255.192    | 192.168.39.146 - 192.168.39.206 | 60    |

## Biblioteca Central (Red 192.168.100.0/24)

| VLAN           | Subred              | Gateway          | Máscara            | Rango de IPs útiles              | Hosts |
|----------------|---------------------|------------------|--------------------|----------------------------------|-------|
| WEB_SERVERS    | 192.168.100.0/25    | 192.168.100.1    | 255.255.255.128    | 192.168.100.2 - 192.168.100.126  | 126   |
| DHCP_SERVERS   | 192.168.100.128/25  | 192.168.100.129  | 255.255.255.128    | 192.168.100.130 - 192.168.100.254| 126   |

# HSRP

conf t
interface Gi0/1
 ip address 192.168.39.1 255.255.255.240
 standby 1 ip 192.168.39.2
 standby 1 priority 110
 standby 1 preempt
 standby 1 track Gi0/0
exit


conf t
interface Gi0/1
 ip address 192.168.39.65 255.255.255.192
 standby 1 ip 192.168.39.2
 standby 1 priority 100
 standby 1 preempt
 standby 1 track Gi0/0
exit


