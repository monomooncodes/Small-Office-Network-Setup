# Small-Office-Network-Setup
Created and configured a basic network for a small office with internet access and security.

# Network Connectivity, VLAN Configuration, and Security Project

## Project Overview
This project involves setting up a secure VLAN-based network, configuring inter-VLAN routing, testing connectivity, and implementing security features such as SSH access.

---

## Objectives
1. Segment traffic using VLANs.
2. Configure inter-VLAN routing with a Cisco router.
3. Test and validate connectivity.
4. Enhance network security with SSH and local authentication.

---

## Topology Overview
### Devices Used
- **Cisco Router**
- **Cisco Switches** (Access and Core)
- **PCs:**
  - VLAN 10 (DeptA): PC0, PC1, PC2
  - VLAN 20 (DeptB): PC3, PC4, PC5

### IP Addressing Scheme
| VLAN | Subnet          | Gateway        | Devices         |
|------|-----------------|----------------|-----------------|
| 10   | 192.168.10.0/24 | 192.168.10.1   | PC0, PC1, PC2   |
| 20   | 192.168.20.0/24 | 192.168.20.1   | PC3, PC4, PC5   |

---

## Configuration Details

### VLAN Configuration (Switch)
```bash
Switch> enable
Switch# configure terminal
Switch(config)# vlan 10
Switch(config-vlan)# name DeptA
Switch(config)# vlan 20
Switch(config-vlan)# name DeptB

Switch(config)# interface range fastEthernet 0/1-3
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 10

Switch(config)# interface range fastEthernet 0/4-6
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 20

Switch(config)# interface fastEthernet 0/24
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20
```

### Router Configuration
```bash
Router> enable
Router# configure terminal
Router(config)# interface g0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0

Router(config)# interface g0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0

Router(config)# exit
Router# write memory
```

### Security Configuration (Router)
```bash
OfficeRouter> enable
OfficeRouter# configure terminal
OfficeRouter(config)# ip domain-name office.local
OfficeRouter(config)# crypto key generate rsa
OfficeRouter(config)# username admin privilege 15 password adminpass
OfficeRouter(config)# line vty 0 4
OfficeRouter(config-line)# login local
OfficeRouter(config-line)# transport input ssh
```

---

## Testing and Validation

### Ping Tests
- **Same VLAN:**
  - PC0 ↔ PC1 (VLAN 10): Successful
  - PC3 ↔ PC4 (VLAN 20): Successful
- **Inter-VLAN:**
  - PC0 (VLAN 10) ↔ PC3 (VLAN 20): Successful (via router)

### Traceroute
To ensure traffic passes through the router:
```bash
tracert 192.168.20.5
```
Result: Shows path through gateways (192.168.10.1 and 192.168.20.1).

### VLAN Verification
```bash
Switch# show vlan brief
Switch# show interfaces trunk
```

### SSH Access Validation
```bash
ssh admin@192.168.10.1
```
Result: Successful secure login.

---

## Challenges and Resolutions
1. **Incorrect IP Configuration:**
   - Issue: PCs in VLAN 10 were not assigned the correct gateway.
   - Resolution: Updated gateway to 192.168.10.1.
2. **Trunk Link Misconfiguration:**
   - Issue: VLAN traffic was not passing between switches.
   - Resolution: Reconfigured trunk port to allow VLANs 10 and 20.
3. **SSH Key Generation Failure:**
   - Issue: RSA key generation failed due to a missing domain name.
   - Resolution: Configured domain name as `office.local`.

---

## Conclusion
This project successfully demonstrated:
1. VLAN configuration for traffic segmentation.
2. Inter-VLAN routing using a Cisco router.
3. Testing and troubleshooting network connectivity.
4. Implementation of SSH for secure access.

These configurations provide a robust framework for managing a VLAN-based network in real-world ISP environments.

---

## References
1. Cisco IOS Command Reference
2. Packet Tracer Simulation Guide
3. Networking Fundamentals by Wendell Odom
