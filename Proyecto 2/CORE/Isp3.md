enable
configure terminal
hostname ISP3
ip routing

! Conexión a ISP1
interface GigabitEthernet1/1/1
 no switchport
 ip address 172.39.0.38 255.255.255.252
 description Enlace a ISP1
 no shutdown
 exit

! Conexión a ISP2
interface GigabitEthernet1/1/2
 no switchport
 ip address 172.39.0.42 255.255.255.252
 description Enlace a ISP2
 no shutdown
 exit

! Configuración BGP básica
router bgp 65003
 bgp router-id 172.39.0.38
 ! ISP1
 neighbor 172.39.0.37 remote-as 65001  
 ! ISP2
 neighbor 172.39.0.41 remote-as 65002  
 network 172.39.0.36 mask 255.255.255.252
 network 172.39.0.40 mask 255.255.255.252
 exit