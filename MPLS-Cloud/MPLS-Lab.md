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
