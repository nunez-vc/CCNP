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
```
MPLS (Multiprotocol Label Switching) is a high performance packet forwarding technology that uses labels instead of traditional IP routing lookups.
```
### R4: PE-San Juan
```
configure terminal
 ip cef
 mpls ip
  interface Ethernet 1/2
  mpls ip
  mpls label protocol ldp
  mpls mtu override 1512
```
### R3: PCore-SanJuan
```
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
 ```
### R2: PCore-Makati
```
configure terminal
 ip cef
 mpls ip
  interface Ethernet 1/1
   mpls ip
   mpls label protocol ldp
   !mpls mtu override 1512
  interface Ethernet 1/2
   mpls ip
   mpls label protocol ldp
   mpls mtu override 1512
```
### R1: PE-Makati
```
configure terminal
 ip cef
 mpls ip
  interface Ethernet 1/0
   mpls ip
   mpls label protocol ldp
   mpls mtu override 1512
```
### How to check:
```
R2#show mpls ldp neighbor (lib)
    Peer LDP Ident: 172.16.1.1:0; Local LDP Ident 172.16.1.2:0
        TCP connection: 172.16.1.1.646 - 172.16.1.2.23066
        State: Oper; Msgs sent/rcvd: 8/8; Downstream
        Up time: 00:00:53
        LDP discovery sources:
          FastEthernet1/1, Src IP addr: 192.168.1.1
        Addresses bound to peer LDP Ident:
          192.168.1.1     172.16.1.1      
    Peer LDP Ident: 172.16.1.3:0; Local LDP Ident 172.16.1.2:0
        TCP connection: 172.16.1.3.41657 - 172.16.1.2.646
        State: Oper; Msgs sent/rcvd: 8/8; Downstream
        Up time: 00:00:40
        LDP discovery sources:
          FastEthernet1/2, Src IP addr: 192.168.1.6
        Addresses bound to peer LDP Ident:
          192.168.1.6     172.16.1.3      

R2#show mpls forwarding-table (fib)
Local  Outgoing    Prefix            Bytes tag  Outgoing   Next Hop    
tag    tag or VC   or Tunnel Id      switched   interface              
16     Pop tag     172.16.1.1/32     0          Fa1/1      192.168.1.1  
17     Pop tag     172.16.1.3/32     0          Fa1/2      192.168.1.6  
SW2P#

R2#show mpls ldp bindings (lfib)
  tib entry: 172.16.1.1/32, rev 2
        local binding:  tag: 16
        remote binding: tsr: 172.16.1.1:0, tag: imp-null
        remote binding: tsr: 172.16.1.3:0, tag: 16
  tib entry: 172.16.1.2/32, rev 6
        local binding:  tag: imp-null
        remote binding: tsr: 172.16.1.1:0, tag: 17
        remote binding: tsr: 172.16.1.3:0, tag: 17
  tib entry: 172.16.1.3/32, rev 4
        local binding:  tag: 17
        remote binding: tsr: 172.16.1.1:0, tag: 16
        remote binding: tsr: 172.16.1.3:0, tag: imp-null
  tib entry: 192.168.1.0/30, rev 8
        local binding:  tag: imp-null
        remote binding: tsr: 172.16.1.1:0, tag: imp-null
        remote binding: tsr: 172.16.1.3:0, tag: 18

R2#show ip route (routing table)
     172.16.0.0/32 is subnetted, 3 subnets
O       172.16.1.1 [110/2] via 192.168.1.1, 00:15:15, FastEthernet1/1
O       172.16.1.3 [110/2] via 192.168.1.6, 00:15:15, FastEthernet1/2
C       172.16.1.2 is directly connected, Loopback0
     192.168.1.0/30 is subnetted, 2 subnets
C       192.168.1.0 is directly connected, FastEthernet1/1
C       192.168.1.4 is directly connected, FastEthernet1/2
```
## Phase 3: VRF Configuration
```
Virtual Router Forwarder: "Virtual/Logical Router" allows you to have overlapping VPN networks.
RD (Route Distinguisher) - makes routes unique inside MPLS VPN.
RT (Route-target) - controls route import/exoirt between VRFs.
```
### R4: PE-SanJuan
```
configure terminal
 ip vrf clientBDOsanjuan
  rd 999:1
  route-target 64999:1
 ip vrf clientBPIsanjuan
  rd 999:2
  route-target 64999:2
 ip vrf clientPNBsanjuan
  rd 999:3
  route-target 64999:3
 ip vrf clientBPOsanjuan
  rd 888:4
  route-target 69666:4
 ip vrf clientBSPsanjuan
  rd 888:5
  route-target 69666:5
```
### R1: PE-Makati
```
configure terminal
 ip vrf clientBDOmakati
  rd 999:1
  route-target 64999:1
 ip vrf clientBPImakati
  rd 999:2
  route-target 64999:2
 ip vrf clientPNBmakati
  rd 999:3
  route-target 64999:3
 ip vrf clientBPOmakati
  rd 888:4
  route-target 69666:4
 ip vrf clientBSPmakati
  rd 888:5
  route-target 69666:5
```
### How to Check
```
R4#show ip vrf
  Name                             Default RD          Interfaces
  clientBDOa                       999:1               
  clientBPIa                       999:2               
  clientPNBa                       999:3               

R1#show ip vrf
  Name                             Default RD          Interfaces
  clientBDOb                       999:1               
  clientBPIb                       999:2               
  clientPNBb                       999:3    
```

## Phase 4: Configuring VRF interfaces on Provider Edge Routers
### R4: PE-SanJuan
```
configure terminal
 interface Ethernet1/1
   ip vrf forwarding clientBPIsanjuan
   ip address 10.1.1.2 255.255.255.252
   no shut
 interface Ethernet1/0
   ip vrf forwarding clientPNBsanjuan
   ip address 10.1.1.2 255.255.255.252
   no shut
 interface Ethernet0/1
   ip vrf forwarding clientBSPsanjuan
   ip address 10.1.1.2 255.255.255.252
   no shut
 interface Ethernet0/0
   ip vrf forwarding clientBPOsanjuan
   ip address 10.1.1.2 255.255.255.252
   no shut
end
```
### R1: PE-Makati
```
configure terminal
 interface Ethernet1/2
   ip vrf forwarding clientBPImakati
   ip address 10.1.1.6 255.255.255.252
   no shut
 interface Ethernet1/1
   ip vrf forwarding clientPNBmakati
   ip address 10.1.1.6 255.255.255.252
   no shut
 interface Ethernet0/1
   ip vrf forwarding clientBSPmakati
   ip address 10.1.1.6 255.255.255.252
   no shut
 interface Ethernet0/0
   ip vrf forwarding clientBPOmakati
   ip address 10.1.1.6 255.255.255.252
   no shut
end
```
### How to check:
```
R1#sh ip int br
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet1/0                10.1.1.1        YES NVRAM  up                    up      
Ethernet1/1                10.1.1.6        YES manual up                    up      
Ethernet1/2                10.1.1.6        YES manual up                    up      
Ethernet1/3                209.9.9.1       YES NVRAM  up                    up      
Loopback0                  172.16.1.1      YES manual up                    up   

Note: Make sure Real routers, can ping Virtual Routers!
```
