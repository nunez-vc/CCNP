# 🏗️ MPLS Cloud Lab - Device Pre-Configurations

*Last Updated: 2026-05-11*  
*Lab Environment: CCNP MPLS Cloud Lab*  
*Purpose: Store device pre-configurations for MPLS lab deployments*

---
&nbsp;

## 🔧 All Devices

Base configuration applied to all devices in the lab:

```
conf t
 enable secret pass
 no logging con
 no ip domain-lookup
 line con 0
  exec-timeout 0 0
  exit
 line vty 0 4
  no login
  exec-timeout 0 0
  transport input all
  exit
end
```

---
&nbsp;

## 🏢 SAN JUAN LOCATION

### BPO - SAN JUAN

```
!@BPO-SanJuan
conf t
 hostname BPO-SanJuan
 ip host sanjuan.BPO.net 10.1.1.1
 ip host makati.BPO.net 10.1.1.5
 int e0/0
  ip add 10.1.1.1 255.255.255.252
  no shut
 end
wr
```

### PNB - SAN JUAN

```
!@PNB-SanJuan
conf t
 hostname PNB-SanJuan
 ip host sanjuan.PNB.net 10.1.1.1
 ip host makati.PNB.net 10.1.1.5
 int e1/1
  ip add 10.1.1.1 255.255.255.252
  no shut
 end
wr
```

### BPI - SAN JUAN

```
!@BPI-SanJuan
conf t
 hostname BPI-SanJuan
 ip host sanjuan.BPI.net 10.1.1.1
 ip host makati.BPI.net 10.1.1.5
 int e1/1
  ip add 10.1.1.1 255.255.255.252
  no shut
 end
wr
```

### BSP - SAN JUAN

```
!@BSP-SanJuan
conf t
 hostname BSP-SanJuan
 ip host sanjuan.BSP.net 10.1.1.1
 ip host makati.BSP.net 10.1.1.5
 int e0/1
  ip add 10.1.1.1 255.255.255.252
  no shut
 end
wr
```

### PROVIDER EDGE (SAN JUAN)

```
!@PE-SanJuan
conf t
 hostname PE-SanJuan
 int lo0
  ip add 172.16.1.4 255.255.255.255
 int e1/2
  ip add 10.1.1.10 255.255.255.252
  no shut
 end
wr
```

### PROVIDER CORE SWITCH (SAN JUAN)

```
!@PCore-SanJuan
conf t
 hostname PCore-SanJuan
 int lo0
  ip add 172.16.1.3 255.255.255.255
 int e1/2
  ip add 10.1.1.9 255.255.255.252
  no shut
 int e1/1
  ip add 10.1.1.6 255.255.255.252
  no shut
 end
wr
```

---
&nbsp;

## 🏢 MAKATI LOCATION

### BPO - MAKATI

```
!@BPO-Makati
conf t
 hostname BPO-Makati
 ip host sanjuan.BPO.net 10.1.1.1
 ip host makati.BPO.net 10.1.1.5
 int e0/0
  ip add 10.1.1.5 255.255.255.252
  no shut
 end
wr
```

### PNB - MAKATI

```
!@PNB-Makati
conf t
 hostname PNB-Makati
 ip host sanjuan.PNB.net 10.1.1.1
 ip host makati.PNB.net 10.1.1.5
 int e1/3
  ip add 10.1.1.5 255.255.255.252
  no shut
 end
wr
```

### BPI - MAKATI

```
!@BPI-Makati
conf t
 hostname BPI-Makati
 ip host sanjuan.BPI.net 10.1.1.1
 ip host makati.BPI.net 10.1.1.5
 int e1/3
  ip add 10.1.1.5 255.255.255.252
  no shut
 end
wr
```

### BSP - MAKATI

```
!@BSP-Makati
conf t
 hostname BSP-Makati
 ip host sanjuan.BSP.net 10.1.1.1
 ip host makati.BSP.net 10.1.1.5
 int e0/1
  ip add 10.1.1.5 255.255.255.252
  no shut
 end
wr
```

### PROVIDER CORE SWITCH (MAKATI)

```
!@PCore-Makati
conf t
 hostname PCore-Makati
 int lo0
  ip add 172.16.1.2 255.255.255.255
 int e1/1
  ip add 10.1.1.5 255.255.255.252
  no shut
 int e1/2
  ip add 10.1.1.2 255.255.255.252
  no shut
 end
wr
```

### PROVIDER EDGE (MAKATI)

```
!@PE-Makati
conf t
 hostname PE-Makati
 int lo0
  ip add 172.16.1.1 255.255.255.255
 int e1/0
  ip add 10.1.1.1 255.255.255.252
  no shut
 end
wr
```
