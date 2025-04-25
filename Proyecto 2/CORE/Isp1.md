enable
configure terminal
hostname ISP1
ip routing

! Conexión a ISP2
interface GigabitEthernet1/1/1
 no switchport
 ip address 172.39.0.33 255.255.255.252
 description Enlace a ISP2
 no shutdown
 exit

! Conexión a ISP3
interface GigabitEthernet1/1/2
 no switchport
 ip address 172.39.0.37 255.255.255.252
 description Enlace a ISP3
 no shutdown
 exit

! Configurar interfaz de conexión a R0
interface GigabitEthernet1/0/1
 no switchport
 ip address 172.39.0.50 255.255.255.252
 description Enlace a R0-TelecomUno
 no shutdown
exit

! Configuración BGP
router bgp 65001
 bgp router-id 172.39.0.33
 ! ISP2
 neighbor 172.39.0.34 remote-as 65002  
 ! ISP3
 neighbor 172.39.0.38 remote-as 65003  
 ! R0
 neighbor 172.39.0.49 remote-as 65001  
 network 172.39.0.32 mask 255.255.255.252
 network 172.39.0.36 mask 255.255.255.252 
 network 172.39.0.48 mask 255.255.255.252
 redistribute eigrp 100
exit

router eigrp 100
 network 172.39.0.48 0.0.0.3
 passive-interface default
 no passive-interface GigabitEthernet1/0/1
 redistribute bgp 65001 metric 10000 100 255 1 1500
 no auto-summary
exit