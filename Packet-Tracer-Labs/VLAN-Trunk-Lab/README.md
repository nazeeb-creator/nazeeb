# VLAN Trunk Lab — Troubleshooting & Configuration

## Objective
Configure VLANs across two switches using a trunk link
and verify connectivity between PCs in same VLAN

## Topology
<img width="987" height="411" alt="Trunk" src="https://github.com/user-attachments/assets/52b4245c-7d1e-48c4-b716-e589c93c83a7" />


Switch0 ----Trunk Link---- Switch1
PC1 - VLAN 10             PC3 - VLAN 10
PC2 - VLAN 20             PC4 - VLAN 20

## Devices Used
- 2 Cisco Switches
- 4 PCs

## IP Address Table
| Device | VLAN | IP Address     | Subnet Mask     |
|--------|------|----------------|-----------------|
| PC1    | 10   | 192.168.10.1   | 255.255.255.0   |
| PC2    | 20   | 192.168.20.1   | 255.255.255.0   |
| PC3    | 10   | 192.168.10.2   | 255.255.255.0   |
| PC4    | 20   | 192.168.20.2   | 255.255.255.0   |

## Configuration Commands

### Switch 0 — VLAN Configuration
Switch1(config)# vlan 10
Switch1(config-vlan)# name VLAN10
Switch1(config)# vlan 20
Switch1(config-vlan)# name VLAN20

### Switch 0 — Access Ports
Switch1(config)# interface fastethernet 0/1
Switch1(config-if)# switchport mode access
Switch1(config-if)# switchport access vlan 10

Switch1(config)# interface fastethernet 0/2
Switch1(config-if)# switchport mode access
Switch1(config-if)# switchport access vlan 20

### Switch 0 — Trunk Port
Switch1(config)# interface fastethernet 0/24
Switch1(config-if)# switchport mode trunk
Switch1(config-if)# switchport trunk allowed vlan 10,20

## Verification Commands
show vlan brief
show interfaces trunk
show mac address-table

## Troubleshooting — What Went Wrong

### Problem
PC1 and PC3 could not ping each other
even though they were in same VLAN 10

### Root Cause
PCs in same VLAN were given different subnets

| PC  | VLAN | Wrong IP     | Correct IP    |
|-----|------|--------------|---------------|
| PC1 | 10   | 192.168.1.2  | 192.168.10.1  |
| PC3 | 10   | 192.168.2.2  | 192.168.10.2  |
| PC2 | 20   | 192.168.1.4  | 192.168.20.1  |
| PC4 | 20   | 192.168.2.3  | 192.168.20.2  |

### Fix
Changed all PC IPs so that same VLAN PCs
are in the same subnet

### Result
<img width="617" height="334" alt="ping" src="https://github.com/user-attachments/assets/d2c05359-155d-4a40-8ff9-8d9501c9bd6d" />


## Key Lessons Learned
1. Same VLAN must have same subnet to communicate
2. Trunk link carries traffic for multiple VLANs
3. VLANs on different switches connect through trunk
4. Different VLANs need a router to communicate

## Key Rule to Remember
Same VLAN + Same Subnet = Ping works 
Same VLAN + Different Subnet = Ping fails 
Different VLAN = Needs router 
