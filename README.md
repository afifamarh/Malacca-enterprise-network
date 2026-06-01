# Malacca-enterprise-network

# Enterprise Network Infrastructure Design – Malacca Branch

## Project Overview

This project demonstrates the design, implementation, and security hardening of the Malacca Branch enterprise network using Cisco Packet Tracer.

The network was designed to provide:

- Secure communication between departments
- VLAN segmentation
- Dynamic routing
- IP Telephony services
- Syslog monitoring
- TFTP support
- Redundancy and failover
- Layer 2 security
- Secure remote management through SSH and AAA

---

# Network Topology

The Malacca Branch consists of:

## Core Devices

- Malacca-CoreSW
- Malacca-Router1
- Malacca-Router2

## Access Layer Devices

- Malacca-ManagementSW1
- Malacca-ManagementSW2
- Malacca-DevelopmentSW1
- Malacca-DevelopmentSW2

## End Devices

### VLAN 40 – Management Department

- Management PCs
- Syslog Server

### VLAN 50 – Development Department

- Development PCs

### VLAN 60 – Voice Network

- IP Phone 2001
- IP Phone 2002

---

# IP Addressing Scheme

| VLAN | Department | Network |
|--------|-----------|-----------|
| VLAN 40 | Management | 172.16.10.0/23 |
| VLAN 50 | Development | 172.16.12.0/25 |
| VLAN 60 | Voice | 172.16.60.0/24 |

---

# Technologies Implemented

## VLAN Segmentation

Network traffic is separated into different VLANs to improve security and reduce broadcast traffic.

### VLAN 40
Management Department

### VLAN 50
Development Department

### VLAN 60
Voice Network

### VLAN 99
Native VLAN

---

# Inter-VLAN Routing

Router-on-a-Stick architecture is implemented through:

Malacca-Router2

Subinterfaces:

- Fa0/1.60

This enables communication between VLANs while maintaining logical separation.

---

# Dynamic Routing

## OSPF

OSPF Area 0 is configured between:

- Core Switch
- Routers

Purpose:

- Automatic route exchange
- Faster convergence
- Scalability

Verification Command:

```bash
show ip route
```

Example Output:

```bash
O E2 172.16.20.0/23
O E2 10.10.0.0/23
O E2 192.168.0.0/23
```

This proves routes are learned dynamically through OSPF.

---

# BGP Configuration

Malacca-Router2 participates in BGP routing.

### Local ASN

65001

### Neighbors

200.200.200.2

200.200.200.6

Purpose:

- Exchange routes with external networks
- Simulate WAN connectivity

Verification Command:

```bash
show ip bgp summary
```

---

# DHCP Services

DHCP is configured for Voice VLAN.

### DHCP Pool

VOICE_V60

### Network

172.16.60.0/24

### Default Gateway

172.16.60.1

### TFTP Option

172.16.60.2

Verification Command:

```bash
show ip dhcp binding
```

---

# IP Telephony System

One of the major components of this project is the implementation of Cisco IP Telephony.

The IP Phone deployment allows voice communication within the organization using VoIP technology.

### IP Phones

| Extension | Device |
|------------|-----------|
| 2001 | IP Phone 1 |
| 2002 | IP Phone 2 |

### Telephony Server

Malacca-Router2 acts as the CME (Cisco Call Manager Express).

### Telephony Configuration

```bash
telephony-service
 max-ephones 10
 max-dn 10
 ip source-address 172.16.60.2
 auto assign 1 to 10
```

### Directory Numbers

```bash
ephone-dn 1
 number 2001

ephone-dn 2
 number 2002
```

### Call Routing

```bash
dial-peer voice 1 voip
 destination-pattern 2001
 session target ipv4:172.16.60.3
```

### Benefits

- Internal voice communication
- Reduced communication cost
- Centralized voice management
- Enterprise-ready infrastructure

Verification Command:

```bash
show ephone
```

Expected Output:

```bash
ephone-1
 extension 2001

ephone-2
 extension 2002
```

---

# TFTP Server

A TFTP server is deployed to support IP Telephony operations.

Purpose:

- Phone firmware delivery
- Configuration file storage
- Backup repository

Benefits:

- Automatic phone provisioning
- Centralized management

Verification:

Open the server and navigate to:

Services → TFTP

---

# Syslog Server

A centralized Syslog Server is implemented for monitoring and auditing.

Purpose:

- Record security events
- Record configuration changes
- Troubleshooting
- Incident investigation

Configured on:

```bash
logging 172.16.10.20
```

Benefits:

- Centralized logging
- Faster troubleshooting
- Improved visibility

Verification Command:

```bash
show logging
```

Verification on Server:

```text
Services → SYSLOG
```

Example Log:

```text
LINK-3-UPDOWN
LINEPROTO-5-UPDOWN
CONFIG_I
```

---

# EtherChannel

EtherChannel is configured using LACP.

Purpose:

- Increased bandwidth
- Link redundancy
- Load balancing

Verification Command:

```bash
show etherchannel summary
```

Expected Output:

```bash
Po1(SU)
Po2(SU)
Po3(SU)
Po4(SU)
```

---

# Spanning Tree Protocol

Rapid-PVST is implemented to prevent switching loops.

Configuration:

```bash
spanning-tree mode rapid-pvst
```

Root Bridge Configuration:

```bash
spanning-tree vlan 40,50 priority 0
```

Verification Command:

```bash
show spanning-tree
```

Results:

### VLAN 40

Core Switch is Root Bridge

### VLAN 50

Core Switch is Root Bridge

### VLAN 60

Po2 is Root Port

Po4 is Alternate Blocked Port

### VLAN 99

Po2 is Root Port

Po4 is Alternate Blocked Port

Benefits:

- Prevents Layer 2 loops
- Provides redundancy
- Automatic failover

---

# Port Security

Port Security is implemented on ManagementSW1.

Verification Command:

```bash
show port-security
```

Example Output:

```bash
Fa0/7 Restrict
Fa0/8 Restrict
Fa0/9 Restrict
Fa0/10 Restrict
```

Features:

- Sticky MAC
- Restrict mode
- Single MAC per port

Benefits:

- Prevents unauthorized devices
- Mitigates MAC flooding attacks

---

# AAA Authentication

AAA is implemented using local authentication.

Configuration:

```bash
aaa new-model
aaa authentication login MALACCA_AUTH local
aaa authorization exec MALACCA_EXEC local
```

Verification Command:

```bash
show running-config | include aaa
```

Benefits:

- Centralized authentication
- Authorization control
- Improved accountability

---

# Secure Remote Access

SSH Version 2 is configured for secure device management.

Configuration:

```bash
ip domain-name MALACCA.cisco.com
crypto key generate rsa
ip ssh version 2
```

VTY Configuration:

```bash
line vty 0 4
 transport input ssh
 login authentication MALACCA_AUTH
```

Verification Command:

```bash
show ip ssh
```

Benefits:

- Encrypted remote access
- Protection against credential interception

---

# Security Features Implemented

### AAA Authentication
✔ Enabled

### SSH Version 2
✔ Enabled

### Port Security
✔ Enabled

### BPDU Guard
✔ Enabled

### DHCP Snooping
✔ Enabled

### VLAN Segmentation
✔ Enabled

### Syslog Monitoring
✔ Enabled

### Unused Port Shutdown
✔ Enabled

---

# Testing Performed

## VLAN Connectivity

✔ Successful

## OSPF Route Learning

✔ Successful

## BGP Neighbor Formation

✔ Successful

## DHCP Address Assignment

✔ Successful

## IP Phone Registration

✔ Successful

## Internal Voice Calls

✔ Successful

## Syslog Event Collection

✔ Successful

## SSH Remote Access

✔ Successful

## Port Security Validation

✔ Successful

---

# Learning Outcomes

This project demonstrates practical implementation of:

- Enterprise Network Design
- VLAN Segmentation
- OSPF Routing
- BGP Routing
- EtherChannel
- Rapid PVST
- IP Telephony
- Syslog Monitoring
- TFTP Services
- AAA Authentication
- SSH Security
- Port Security
- DHCP Services

---

# Author

Mohd Afif Ammarhazim Bin Mohd Nazariee

APU – Bachelor of Computer Science (Cyber Security)

Cisco Packet Tracer Enterprise Network Project
