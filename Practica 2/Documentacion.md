# Topologia 



# Vlan

Nombre             | Vlan
-------------------|---------------
ADMIN              |  13       
ESTUDIANTES        |  23    
WEB_SERVERS        |  33    
DHCP_SERVER        |  43    

#  Direccionamiento IP

Subred                  | Rango de IPs                        | Máscara          | Hosts disponibles
------------------------|-------------------------------------|------------------|------------------
192.168.39.0/28         | 192.168.39.1 - 192.168.39.14        | 255.255.255.240  | 14 hosts      
192.168.39.64/26        | 192.168.39.65 - 192.168.39.126      | 255.255.255.192  | 62 hosts      
192.168.39.128/26       | 192.168.39.129 - 192.168.39.190     | 255.255.255.192  | 62 hosts      
192.168.39.192/26       | 192.168.39.193 - 192.168.39.254     | 255.255.255.192  | 62 hosts      



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