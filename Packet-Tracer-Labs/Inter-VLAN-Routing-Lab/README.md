# Inter-VLAN Routing Lab — Router on a Stick

## Objective
Configure Inter-VLAN routing using router subinterfaces
so that PCs in different VLANs can communicate with each other

## Topology
<img width="808" height="423" alt="Topology " src="https://github.com/user-attachments/assets/a53c3eef-a8f0-4545-ae87-23814792659a" />

PC1 (VLAN 10) ──┐
PC2 (VLAN 10) ──┤
Switch (F0/20 Trunk) ── Router G0/0
PC3 (VLAN 20) ──┤                       ├── G0/0.10 (192.168.1.100)
PC4 (VLAN 20) ──┘                       └── G0/0.20 (192.168.2.100)

## Devices Used
- 1 Cisco Router (1941 or 2911)
- 1 Cisco Switch (2960)
- 4 PCs

## IP Address Table
| Device    | VLAN | IP Address     | Subnet Mask   | Gateway        |
|-----------|------|----------------|---------------|----------------|
| PC1       | 10   | 192.168.1.1    | 255.255.255.0 | 192.168.1.100  |
| PC2       | 10   | 192.168.1.2    | 255.255.255.0 | 192.168.1.100  |
| PC3       | 20   | 192.168.2.1    | 255.255.255.0 | 192.168.2.100  |
| PC4       | 20   | 192.168.2.2    | 255.255.255.0 | 192.168.2.100  |
| G0/0.10   | 10   | 192.168.1.100  | 255.255.255.0 | —              |
| G0/0.20   | 20   | 192.168.2.100  | 255.255.255.0 | —              |

---

## Configuration Commands

### Switch — VLAN Configuration
Switch(config)# vlan 10
Switch(config-vlan)# name VLAN10
Switch(config)# vlan 20
Switch(config-vlan)# name VLAN20

### Switch — Access Ports
! PC1
Switch(config)# interface fastethernet 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
! PC2
Switch(config)# interface fastethernet 0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
! PC3
Switch(config)# interface fastethernet 0/3
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20
! PC4
Switch(config)# interface fastethernet 0/4
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20

### Switch — Trunk Port to Router
Switch(config)# interface fastethernet 0/20
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20

### Router — Subinterface Configuration
! Enable main interface first
Router(config)# interface gigabitethernet 0/0
Router(config-if)# no shutdown
! Subinterface for VLAN 10
Router(config)# interface gigabitethernet 0/0.10
Router(config-subif)# encapsulation dot1q 10
Router(config-subif)# ip address 192.168.1.100 255.255.255.0
! Subinterface for VLAN 20
Router(config)# interface gigabitethernet 0/0.20
Router(config-subif)# encapsulation dot1q 20
Router(config-subif)# ip address 192.168.2.100 255.255.255.0

---

## Verification Commands
! Router
show ip interface brief
show running-config
! Switch
show vlan brief
show interfaces trunk

---

## Ping Test Results
<img width="642" height="304" alt="Ping" src="https://github.com/user-attachments/assets/d761453b-f81e-4496-9d9f-4ae99f88da99" />


### Test Order Used
| Test | From | To | Result |
|------|------|----|--------|
| 1 | PC1 | 192.168.1.100 (gateway) | ✅ |
| 2 | PC3 | 192.168.2.100 (gateway) | ✅ |
| 3 | PC1 | PC3 (cross VLAN) | ✅ |
| 4 | PC1 | PC4 (cross VLAN) | ✅ |
| 5 | PC2 | PC3 (cross VLAN) | ✅ |

---

## Troubleshooting Done
| Problem | Cause | Fix |
|---------|-------|-----|
| Ping failing initially | Configuration issue | Verified all steps and fixed |

---

## Key Lessons Learned
1. Main interface must be enabled with no shutdown first
2. Subinterface encapsulation VLAN must match VLAN number exactly
3. Each PC gateway must point to its VLAN subinterface IP
4. Trunk port on switch must allow both VLANs
5. Without router VLANs cannot communicate

## Key Rule to Remember
Same VLAN = Switch handles it 
Different VLAN = Router needed 
Router on a Stick = One physical link with subinterfaces 
