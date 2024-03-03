# Setting Up VLANs

### Objective
The objective of this lab is to configure a network with multiple VLANs on one network, with each VLAN representing a different department (sales, marketing and accounting).

     -- Setup --
     Devices Needed: 1 router, 1 switch, 9 computers (3 for each VLAN).
     Connections: Connect the switch to the router using an Ethernet cable. Connect each computer to a switch port.

### Topology Setup:
- Open Packet Tracer and locate the router, switch, and computers in the device list near the bottom. <img width="500" alt="device selection" src="https://github.com/0xbythesecond/Setting_Up_VLAN/assets/23303634/88533caa-35f0-4587-895a-f03b76f07087"/>

- Drag and drop the router, switch, and nine computers (CTRL + click the computer to add multiples) onto the workspace.
- Use straight-through Ethernet cables to connect the switch to the router's Ethernet interface (usually labeled as GigabitEthernet0/0 or Gig0/0/0).
- Connect each computer to a switch port.
- Pictured below is the topology that we will be working with. I've decided to provide a background color so that each VLAN is representative of the differing departments on the switch.
 - <img width="500" alt="initial topology" src="https://github.com/0xbythesecond/Setting_Up_VLAN/assets/23303634/9f316d3d-3567-4012-b9a6-fbedaf65f048"/>
 

### To start we will need to configure the Router interface for each of the VLANs.
- We need to access the CLI on the Router to configure the different VLANS that we require. Select the `CLI` tab at the top once the router is chosen.
  - <img alt="select router cli" width="400" src="https://github.com/0xbythesecond/Setting_Up_VLAN/assets/23303634/8d25c478-6a38-4815-bba9-8a42f9eaf95d"/>

- While on the Router's CLI, enter global configuration mode by typing `enable` and then `configure terminal`.
- Create subinterfaces on the router's Ethernet interface for each VLAN.
  - > Note: It's a logical interface created within a physical interface of a router. It allows the router to support multiple virtual LANs (VLANs) or subnets on a single physical interface. Subinterfaces are typically used when a router needs to connect to multiple networks, each with its own subnet or VLAN. 
    
 ```c
For VLAN 10 (accounting): --> Router(config)# interface gigabitethernet0/0.10
For VLAN 20 (sales): --> Router(config)# interface gigabitethernet0/0.20
For VLAN 30 (marketing): --> Router(config)# interface gigabitethernet0/0.30
 ```

- Enable dot1Q for the different VLANs.
- This encapsulation method allows the router to understand and process VLAN-tagged frames that are received on the trunk link from the switch.
```c
For VLAN 10 (accounting): --> Router(config): encapsulation dot1Q 10
For VLAN 20 (sales): --> Router(config): encapsulation dot1Q 20
For VLAN 30 (marketing): --> Router(config): encapsulation dot1Q 30
   
- Assign an IP address to each subinterface within the respective VLAN range:

```c
 For VLAN 10 (accounting): ip address 192.168.10.1 255.255.255.0
 VLAN 20 (sales): ip address 192.168.20.1 255.255.255.0
 VLAN 30 (marketing): ip address 192.168.30.1 255.255.255.0
 ```

Enable each subinterface by typing `no shutdown`.

### Configuration of Switch:
- Click on the switch and select "CLI" to access its configuration mode.
- Create VLANs on the switch for sales, marketing, and accounting:
  
```c
Switch(config)# vlan 10
Switch(config)# name Sales
Switch(config)# vlan 20
Switch(config)# name Accounting
Switch(config)# vlan 30
Switch(config)# name Marketing
```

Assign switch ports to the appropriate VLAN using the interface and switchport access vlan commands.

```c
Switch(config)# interface range fastethernet0/1 - 3
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 10
```

Repeat the above steps for VLANs 20 and 30, assigning ports to the corresponding VLANs.

```c
Switch(config)# interface fastethernet0/11
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed 10,20,30
```

Allowing VLANs 10, 20, and 30 across the trunk link connecting to the router enables traffic from these VLANs to traverse the link and reach the router. Specifically, configuring the switch port connected to the router as a trunk port. The trunk link connecting to the router enables efficient communication between devices in these VLANs and facilitates inter-VLAN routing, contributing to a well-segmented and organized network architecture.

### Assigning IP Addresses to Computers:

- Click on each computer and access its configuration panel.
- Assign IP addresses to the computers within each VLAN:
  
  For VLAN 10 (sales):
  
```c
  Computer 1: IP address: 192.168.10.2, Subnet mask: 255.255.255.0, Default gateway: 192.168.10.1
  Computer 2: IP address: 192.168.10.3, Subnet mask: 255.255.255.0, Default gateway: 192.168.10.1
  Computer 3: IP address: 192.168.10.4, Subnet mask: 255.255.255.0, Default gateway: 192.168.10.1
```
Repeat the above steps for VLANs 20 and 30, assigning appropriate IP addresses to computers in each VLAN.

Testing Connectivity Within VLANs:
- Test connectivity between computers within the same VLAN using the ping command.
- Ensure that computers in the same VLAN can communicate with each other.

Verification of Inter-VLAN Routing:
- Use the show `ip route` command in router CLI to verify that the router has learned routes for each VLAN subnet.

Testing Inter-VLAN Connectivity (Optional):
- Test connectivity between computers in different VLANs by pinging a computer from one VLAN to another.

