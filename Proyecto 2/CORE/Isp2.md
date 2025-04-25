enable
configure terminal
hostname ISP2
ip routing

! Conexión a ISP1
interface GigabitEthernet1/1/1
 no switchport
 ip address 172.39.0.34 255.255.255.252
 description Enlace a ISP1
 no shutdown
 exit

! Conexión a ISP3
interface GigabitEthernet1/1/2
 no switchport
 ip address 172.39.0.41 255.255.255.252
 description Enlace a ISP3
 no shutdown
 exit

! Configuración BGP básica
router bgp 65002
 bgp router-id 172.39.0.34
 ! ISP1
 neighbor 172.39.0.33 remote-as 65001  
 ! ISP3
 neighbor 172.39.0.42 remote-as 65003  
 network 172.39.0.32 mask 255.255.255.252
 network 172.39.0.40 mask 255.255.255.252
 exit