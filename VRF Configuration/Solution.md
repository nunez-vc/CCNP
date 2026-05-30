# Solution
## 1. Extend the ENCOR VRF between R10 and R20 using Tunnel0
### R10
```
configure terminal
  interface Tunnel 0
    tunnel source Ethernet0/1   ! use "source" command below if the initial config does not have one
    tunnel destination 10.10.2.1
    ! use below command if the IP address or VRF ENCOR is missing
    ip vrf forwarding ENCOR
    ip add 10.10.10.1 255.255.255.0
    end
write    
```
### R20
```
configure terminal
  interface Tunnel 0
    tunnel source Ethernet0/2
    tunnel destination 10.10.1.1
    ip vrf forwarding ENCOR
    ip add 10.100.100.2 255.255.255.0
    end
write
```
### How to verify:
```
Access R10 and try pinging R20s Tunnel0 via VRF ENCOR (you have to wait for about 1 minute before the ping to work)

R10#ping vrf ENCOR 10.100.100.2

Ping should be successful.
```

## 2. Protect Tunnel0 using the preconfigured profile. Use the 'show run" command to identify the preconfigured profle.
### R10
```
configure terminal
  interface Tunnel 0
    tunnel protection ipsec profile MYPROFILE
    end
write
```
### R20
```
configure terminal
  interface Tunnel 0
    tunnel protection ipsec profile MYPROFILE
    end
write
```

## 3. Configure static routing on R10 and R20 so that users in VLAN 100 and VLAN 101 that belong to the ENCOR VRF are able to communicate with each other.
```
Note: Tunnel0 should be the only interface used to route traffic for the ENCOR VRF.
```
### R10
```
configure terminal
  ip route vrf ENCOR 10.11.2.0 255.255.255.0 Tunnel0
  end
write
```
### R20
```
configure terminal
  ip route vrf ENCOR 10.10.1.0 255.255.255.0 Tunnel0
  end
write
```
### How to verify:
```
@R10
R10#ping vrf ENCOR 10.11.2.1 source e0/0.100

@R20
R20#ping vrf ENCOR 10.10.1.1 source e0/0.101
```

## Save the configurations: Do not forget this on exam
```
In both R10 and R20
R10#copy running-config startup-config
R20#copy running-config startup-config
```
