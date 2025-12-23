# 🏢 Project: TexAuto Logistics Network Infrastructure
## Day 1: Site Architecture & Network Topology Design (DAL-01)

![Status](https://img.shields.io/badge/Status-Complete-success) ![Tool](https://img.shields.io/badge/Tool-Cisco_Packet_Tracer-blue) ![Role](https://img.shields.io/badge/Role-Infrastructure_Engineer-orange)

### 📋 Scenario
**TexAuto Logistics** is expanding its operations in Texas with a new Inspection & Reconditioning Center (IRC) in **Dallas (DAL-01)**. As the Lead Field Infrastructure Engineer, I am tasked with designing a scalable, standardized network topology that can be replicated across future sites in Houston and Austin.

This project simulates a **Cisco Meraki** full-stack deployment using **Cisco Packet Tracer** to validate the underlying routing, switching, and VLAN segmentation logic before physical implementation.

---

### 🎯 Objectives
1.  **Design the Topology:** Create a "Hub" architecture compatible with Meraki hardware specs.
2.  **Layer 2 Configuration:** Implement VLAN segmentation for Management and Operations.
3.  **Layer 3 Configuration:** Configure "Router-on-a-Stick" and DHCP services.
4.  **Verification:** Ensure successful Inter-VLAN routing and end-to-end connectivity.

---

### 🛠️ Lab Environment & Hardware
* **Simulator:** Cisco Packet Tracer 8.x
* **Router (Gateway):** Cisco ISR 4331 (Simulating **Meraki MX64**)
* **Switch (Core):** Cisco 2960-24TT (Simulating **Meraki MS220**)
* **Access Point:** Cisco AP-PT (Simulating **Meraki MR42**)
* **End Points:** Admin PC, Sales Laptop.

### 📝 Operations Workflow (Agile/ITIL)
* **Trello Board:** [Card] "Design Network Topology - DAL-01" moved to *In Progress*.
* **Jira Ticket:** `[PROJ-001] Initial Site Standup for Dallas Hub`.

---

### ⚙️ Implementation Guide

#### Phase 1: Physical Topology (Layer 1)
*Standardized cabling ensures consistency across all TexAuto sites.*

| Source Device | Port | Dest Device | Port | Cable Type | Description |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **ISP Cloud** | Coax/Fiber | **DAL-MX-01** | `Gi0/0/0` | Straight-Through | WAN Uplink |
| **DAL-MX-01** | `Gi0/0/1` | **DAL-MS-01** | `Gi0/1` | Straight-Through | LAN Uplink (Trunk) |
| **DAL-MS-01** | `Fa0/1` | **Admin-PC** | NIC | Straight-Through | Mgmt VLAN Access |
| **DAL-MS-01** | `Fa0/2` | **Sales-Laptop**| NIC | Straight-Through | Sales VLAN Access |

#### Phase 2: Switch Configuration (Layer 2)
*Objective: Create VLANs to segregate Management traffic from Sales data.*

**Device:** `DAL-MS-01`
```bash
Enable
Configure Terminal

! --- Create VLAN Database ---
vlan 10
 name MANAGEMENT
vlan 20
 name SALES
exit

! --- Configure Access Ports (End Users) ---
interface fa0/1
 description ADMIN-PC
 switchport mode access
 switchport access vlan 10
 no shutdown

interface fa0/2
 description SALES-LAPTOP
 switchport mode access
 switchport access vlan 20
 no shutdown

! --- Configure Uplink to Router (Trunk) ---
interface gi0/1
 description UPLINK-TO-MX
 switchport mode trunk
 no shutdown
exit
write memory

Enable
Configure Terminal

! --- Configure WAN Interface ---
interface g0/0/0
 description WAN-ISP-LINK
 ip address dhcp
 no shutdown

! --- Configure LAN Physical Interface ---
interface g0/0/1
 no shutdown

! --- Configure Gateway for VLAN 10 (Mgmt) ---
interface g0/0/1.10
 encapsulation dot1Q 10
 ip address 10.1.10.1 255.255.255.0

! --- Configure Gateway for VLAN 20 (Sales) ---
interface g0/0/1.20
 encapsulation dot1Q 20
 ip address 10.1.20.1 255.255.255.0
exit

! --- Configure DHCP Services (Meraki Simulation) ---
ip dhcp pool VLAN-10-POOL
 network 10.1.10.0 255.255.255.0
 default-router 10.1.10.1
 dns-server 8.8.8.8

ip dhcp pool VLAN-20-POOL
 network 10.1.20.0 255.255.255.0
 default-router 10.1.20.1
 dns-server 8.8.8.8
exit
write memory
