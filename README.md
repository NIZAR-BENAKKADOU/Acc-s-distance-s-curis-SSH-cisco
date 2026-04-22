# Accès à distance sécurisé (SSH) 🔐
 
> Cisco Packet Tracer Lab — Secure Remote Access using SSH
 
---
 
## 📋 Description
 
This lab demonstrates how to configure **secure remote access (SSH)** on Cisco devices (Router 2911 and Switches 2960). Telnet is explicitly blocked to enforce security best practices.
 
---
 
## 🗺️ Network Topology
 
```
                        R1 (2911)
                        Gig0/0
                           |
                        S1 (2960)
                       /         \
               Gig0/2             Fa0/24
                 /                     \
           S2 (2960)               S3 (2960)
          /    |    \             /    |    \
        PC0   PC1   PC2        PC3   PC4   PC5
```
 
---
 
## 📌 VLAN Configuration
 
| VLAN | Network | Description |
|------|---------|-------------|
| VLAN 1 | 192.168.0.0/24 | Management (PC Admin) |
| VLAN 10 | 192.168.10.0/24 | PC0, PC3 |
| VLAN 20 | 192.168.20.0/24 | PC1, PC4 |
| VLAN 30 | 192.168.30.0/24 | PC2, PC5 |
 
---
 
## 🖥️ IP Addressing Table
 
| Device | IP Address | Subnet Mask | Default Gateway |
|--------|-----------|-------------|-----------------|
| PC Admin | 192.168.0.254 | 255.255.255.0 | 192.168.0.1 |
| R1 | 192.168.0.1 | 255.255.255.0 | — |
| S1 (SVI) | 192.168.0.2 | 255.255.255.0 | 192.168.0.1 |
| S2 (SVI) | 192.168.0.3 | 255.255.255.0 | 192.168.0.1 |
| S3 (SVI) | 192.168.0.4 | 255.255.255.0 | 192.168.0.1 |
| PC0 | 192.168.10.1 | 255.255.255.0 | 192.168.10.1 |
| PC1 | 192.168.20.1 | 255.255.255.0 | 192.168.20.1 |
| PC2 | 192.168.30.1 | 255.255.255.0 | 192.168.30.1 |
| PC3 | 192.168.10.2 | 255.255.255.0 | 192.168.10.1 |
| PC4 | 192.168.20.2 | 255.255.255.0 | 192.168.20.1 |
| PC5 | 192.168.30.2 | 255.255.255.0 | 192.168.30.1 |
 
---
 
## ⚙️ Device Configuration
 
### Router R1
```bash
enable
configure terminal
hostname R1
enable secret cisco
 
interface GigabitEthernet0/0
ip address 192.168.0.1 255.255.255.0
no shutdown
exit
 
interface GigabitEthernet0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
no shutdown
exit
 
interface GigabitEthernet0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
no shutdown
exit
 
interface GigabitEthernet0/0.30
encapsulation dot1Q 30
ip address 192.168.30.1 255.255.255.0
no shutdown
exit
 
ip domain-name isga.ma
username admin privilege 15 secret cisco
crypto key generate rsa  ! Choose 1024 bits
ip ssh version 2
 
line vty 0 4
transport input ssh
login local
exit
```
 
### Switch S1
```bash
enable
configure terminal
hostname S1
enable secret cisco
 
vlan 10
name VLAN10
exit
vlan 20
name VLAN20
exit
vlan 30
name VLAN30
exit
 
interface vlan 1
ip address 192.168.0.2 255.255.255.0
no shutdown
exit
 
ip default-gateway 192.168.0.1
 
interface GigabitEthernet0/1
switchport mode trunk
exit
interface GigabitEthernet0/2
switchport mode trunk
exit
interface FastEthernet0/24
switchport mode trunk
exit
 
ip domain-name isga.ma
username admin privilege 15 secret cisco
crypto key generate rsa  ! Choose 1024 bits
ip ssh version 2
 
line vty 0 4
transport input ssh
login local
exit
```
 
### Switch S2
```bash
enable
configure terminal
hostname S2
enable secret cisco
 
vlan 10
name VLAN10
exit
vlan 20
name VLAN20
exit
vlan 30
name VLAN30
exit
 
interface vlan 1
ip address 192.168.0.3 255.255.255.0
no shutdown
exit
 
ip default-gateway 192.168.0.1
 
interface GigabitEthernet0/1
switchport mode trunk
exit
 
interface FastEthernet0/2
switchport mode access
switchport access vlan 10
exit
interface FastEthernet0/3
switchport mode access
switchport access vlan 20
exit
interface FastEthernet0/4
switchport mode access
switchport access vlan 30
exit
 
ip domain-name isga.ma
username admin privilege 15 secret cisco
crypto key generate rsa  ! Choose 1024 bits
ip ssh version 2
 
line vty 0 4
transport input ssh
login local
exit
```
 
### Switch S3
```bash
enable
configure terminal
hostname S3
enable secret cisco
 
vlan 10
name VLAN10
exit
vlan 20
name VLAN20
exit
vlan 30
name VLAN30
exit
 
interface vlan 1
ip address 192.168.0.4 255.255.255.0
no shutdown
exit
 
ip default-gateway 192.168.0.1
 
interface GigabitEthernet0/1
switchport mode trunk
exit
 
interface FastEthernet0/1
switchport mode access
switchport access vlan 10
exit
interface FastEthernet0/2
switchport mode access
switchport access vlan 20
exit
interface FastEthernet0/3
switchport mode access
switchport access vlan 30
exit
 
ip domain-name isga.ma
username admin privilege 15 secret cisco
crypto key generate rsa  ! Choose 1024 bits
ip ssh version 2
 
line vty 0 4
transport input ssh
login local
exit
```
 
---
 
## 🧪 Tests Performed
 
### Connectivity Test (from PC Admin)
```
ping 192.168.0.1   ✅ R1
ping 192.168.0.2   ✅ S1
ping 192.168.0.3   ✅ S2
ping 192.168.0.4   ✅ S3
```
 
### SSH Test (from PC Admin)
```
ssh -l admin 192.168.0.1   ✅ R1 - Access granted
ssh -l admin 192.168.0.2   ✅ S1 - Access granted
ssh -l admin 192.168.0.3   ✅ S2 - Access granted
ssh -l admin 192.168.0.4   ✅ S3 - Access granted
```
 
### Telnet Test (from PC Admin)
```
telnet 192.168.0.1   ❌ Refused (SSH only — as expected!)
```
 
---
 
## ✅ Results
 
| Task | Status |
|------|--------|
| Topology built | ✅ |
| VLANs configured | ✅ |
| IP addresses assigned | ✅ |
| Inter-VLAN routing | ✅ |
| SSH configured on all devices | ✅ |
| SSH access tested | ✅ |
| Telnet blocked | ✅ |
 
---
 
## 🛠️ Tools Used
 
- Cisco Packet Tracer
- Cisco 2911 Router
- Cisco 2960 Switches
---
 
## 👤 Author
 
- **Domain:** isga.ma
- **Username:** admin
- **Password:** cisco

> ⚠️ Note: Default password is `cisco` for all devices as per lab instructions.


## 🗺️ Network Topology

![Network topology](images/topology.png)
