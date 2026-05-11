## Phase 1: Enable Basic OSPF Routing
### R4: PE-SanJuan
```
configure terminal
 no router ospf 1
 router ospf 1
  network 172.16.0.0 0.0.255.255 area 0
  network 10.1.1.8 0.0.0.3 area 0
```
### R3: PCore-SanJuan
```
configure terminal
 no router ospf 1
 router ospf 1
  network 172.16.0.0 0.0.255.255 area 0
  network 10.1.1.8 0.0.0.3 area 0
  network 10.1.1.4 0.0.0.3 area 0
````
### R2: PCore-Makati
```
configure terminal
 no router ospf 1
 router ospf 1
  network 172.16.0.0 0.0.255.255 area 0
  network 10.1.1.0 0.0.0.3 area 0
  network 10.1.1.4 0.0.0.3 area 0
```
### R1: PE-Makati 
```
configure terminal
 no router ospf 1
 router ospf 1
  network 172.16.0.0 0.0.255.255 area 0
  network 10.1.1.0 0.0.0.3 area 0
```

## Phase 2: Basic MPLS Configuration
### Commands
```
ip cef --> CEF(Cisco Express Forwarding) - L3 switching technology to increase network speed and scalability.
mpls ip --> enables Multiprotocol Label Switching (MPLS) forwarding for IPv4/IPv6 packets on an interface.
```
### R4: PE-San Juan
configure terminal
 ip cef
 mpls ip
  interface Ethernet 1/2
  mpls ip
  mpls label protocol ldp
  mpls mtu override 1512

### R4: PCore-SanJuan
configure terminal
 ip cef  
 mpls ip
  interface Ethernet 1/1
   mpls ip
   mpls label protocol ldp
   !mpls mtu override 1512
   exit
  interface Ethernet 1/2
   mpls ip
   mpls label protocol ldp
   mpls mtu override 1512
 






















