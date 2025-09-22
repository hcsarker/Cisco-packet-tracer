# SOHO Network Tutorial - Cisco Packet Tracer

## Overview
This tutorial guides you through creating a Small Office/Home Office (SOHO) network using Cisco Packet Tracer. You'll learn to design, configure, and troubleshoot a typical small business network infrastructure.

## Learning Objectives
By completing this tutorial, you will:
- Understand SOHO network topology design
- Configure basic router and switch settings
- Set up DHCP services
- Configure wireless access points
- Implement basic security measures
- Test network connectivity

## Network Requirements
Our SOHO network will include:
- 1 Router (ISR 2911 or similar)
- 1 Switch (2960 or similar)
- 1 Wireless Access Point
- 3-5 PCs/Laptops
- 1 Server (for DHCP/DNS)
- 1 Printer

## Step-by-Step Tutorial

### Step 1: Network Planning
**IP Address Scheme:**
- Network: 192.168.1.0/24
- Router LAN IP: 192.168.1.1
- DHCP Pool: 192.168.1.100-192.168.1.200
- Server: 192.168.1.10
- Printer: 192.168.1.20

### Step 2: Physical Topology Setup

1. **Add Devices to Workspace:**
   - Router: ISR 2911
   - Switch: 2960-24TT
   - Wireless Access Point: WRT300N
   - Server: Server-PT
   - PCs: PC-PT (3-4 units)
   - Laptop: Laptop-PT (1-2 units)
   - Printer: Printer-PT

2. **Physical Connections:**
   ```
   Internet Cloud → Router (G0/0)
   Router (G0/1) → Switch (Fa0/1)
   Switch (Fa0/2) → Server
   Switch (Fa0/3) → Printer
   Switch (Fa0/4-6) → PCs
   Switch (Fa0/7) → Wireless Access Point
   Wireless → Laptops/Mobile devices
   ```

### Step 3: Router Configuration

```cisco
Router> enable
Router# configure terminal
Router(config)# hostname SOHO-Router
SOHO-Router(config)# 

! Configure WAN interface (to Internet)
SOHO-Router(config)# interface GigabitEthernet0/0
SOHO-Router(config-if)# ip address dhcp
SOHO-Router(config-if)# no shutdown
SOHO-Router(config-if)# exit

! Configure LAN interface
SOHO-Router(config)# interface GigabitEthernet0/1
SOHO-Router(config-if)# ip address 192.168.1.1 255.255.255.0
SOHO-Router(config-if)# no shutdown
SOHO-Router(config-if)# exit

! Configure DHCP Pool
SOHO-Router(config)# ip dhcp pool LAN
SOHO-Router(dhcp-config)# network 192.168.1.0 255.255.255.0
SOHO-Router(dhcp-config)# default-router 192.168.1.1
SOHO-Router(dhcp-config)# dns-server 8.8.8.8 8.8.4.4
SOHO-Router(dhcp-config)# exit

! Exclude static IP addresses from DHCP
SOHO-Router(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.99

! Configure NAT for Internet access
SOHO-Router(config)# access-list 1 permit 192.168.1.0 0.0.0.255
SOHO-Router(config)# ip nat inside source list 1 interface GigabitEthernet0/0 overload
SOHO-Router(config)# interface GigabitEthernet0/0
SOHO-Router(config-if)# ip nat outside
SOHO-Router(config-if)# interface GigabitEthernet0/1
SOHO-Router(config-if)# ip nat inside
SOHO-Router(config-if)# exit

! Save configuration
SOHO-Router(config)# exit
SOHO-Router# write memory
```

### Step 4: Switch Configuration

```cisco
Switch> enable
Switch# configure terminal
Switch(config)# hostname SOHO-Switch
SOHO-Switch(config)# 

! Configure VLANs (optional for advanced setup)
SOHO-Switch(config)# vlan 10
SOHO-Switch(config-vlan)# name DATA
SOHO-Switch(config-vlan)# vlan 20
SOHO-Switch(config-vlan)# name VOICE
SOHO-Switch(config-vlan)# exit

! Configure access ports
SOHO-Switch(config)# interface range FastEthernet0/2-6
SOHO-Switch(config-if-range)# switchport mode access
SOHO-Switch(config-if-range)# switchport access vlan 10
SOHO-Switch(config-if-range)# exit

! Configure trunk port to router
SOHO-Switch(config)# interface FastEthernet0/1
SOHO-Switch(config-if)# switchport mode access
SOHO-Switch(config-if)# exit

! Save configuration
SOHO-Switch(config)# exit
SOHO-Switch# write memory
```

### Step 5: Wireless Access Point Configuration

1. **Access WAP GUI:**
   - Connect PC to switch
   - Open web browser
   - Navigate to WAP IP (usually 192.168.1.245 by default)

2. **Configure Wireless Settings:**
   - Network Name (SSID): SOHO-WiFi
   - Security: WPA2-PSK
   - Password: StrongPassword123
   - Channel: Auto or 6

### Step 6: Server Configuration

1. **Static IP Configuration:**
   - IP: 192.168.1.10
   - Subnet: 255.255.255.0
   - Gateway: 192.168.1.1
   - DNS: 8.8.8.8

2. **Services Configuration:**
   - Enable HTTP service
   - Enable DHCP service (if not using router DHCP)
   - Enable DNS service

### Step 7: Client Configuration

**For PCs:**
1. Set to obtain IP automatically (DHCP)
2. Test connectivity with ping commands
3. Access web services

**For Laptops:**
1. Configure wireless adapter
2. Connect to SOHO-WiFi network
3. Enter wireless password
4. Test connectivity

### Step 8: Testing and Verification

1. **Connectivity Tests:**
   ```
   PC> ipconfig
   PC> ping 192.168.1.1
   PC> ping 8.8.8.8
   PC> ping www.google.com
   ```

2. **DHCP Verification:**
   ```
   SOHO-Router# show ip dhcp binding
   SOHO-Router# show ip dhcp pool
   ```

3. **Wireless Connectivity:**
   - Verify laptops can connect to WiFi
   - Test internet access from wireless devices

## Troubleshooting Common Issues

### No Internet Access
- Check NAT configuration
- Verify default route
- Test DNS resolution

### DHCP Not Working
- Check DHCP pool configuration
- Verify excluded addresses
- Check interface IP helpers

### Wireless Connection Issues
- Verify SSID broadcast
- Check security settings
- Confirm channel settings

## Advanced Configuration (Optional)

### Port Security
```cisco
SOHO-Switch(config)# interface FastEthernet0/2
SOHO-Switch(config-if)# switchport port-security
SOHO-Switch(config-if)# switchport port-security maximum 2
SOHO-Switch(config-if)# switchport port-security violation shutdown
```

### Access Control List (ACL)
```cisco
SOHO-Router(config)# access-list 100 deny tcp any any eq 23
SOHO-Router(config)# access-list 100 permit ip any any
SOHO-Router(config)# interface GigabitEthernet0/1
SOHO-Router(config-if)# ip access-group 100 in
```

## Network Diagram

```
    Internet
       |
   [Router] - 192.168.1.1
       |
   [Switch] - Management
    |  |  |
    |  |  +-- [Server] - 192.168.1.10
    |  |
    |  +-- [Printer] - 192.168.1.20
    |
    +-- [PC1] - DHCP
    +-- [PC2] - DHCP
    +-- [WAP] - WiFi Network
         |
    [Laptop1] [Laptop2] - Wireless DHCP
```

## Summary
This SOHO network tutorial covers:
- Basic router and switch configuration
- DHCP service setup
- Wireless network implementation
- NAT configuration for Internet access
- Basic security measures
- Testing and troubleshooting procedures

## Practice Exercises
1. Add a guest wireless network with different security settings
2. Implement VLANs to separate different types of traffic
3. Configure port security on switch interfaces
4. Set up a web server for internal company website
5. Implement access control lists for security

---
*Created for educational purposes using Cisco Packet Tracer*