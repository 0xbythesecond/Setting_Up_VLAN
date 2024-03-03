# Setting Up VLANs
![VLAN TOPOLOGY](https://github.com/0xbythesecond/Setting_Up_VLAN/assets/23303634/b35a4b26-4dee-4130-8168-309cb2a8b02d)

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
For VLAN 10 (accounting): --> Router(config-if)#: encapsulation dot1Q 10
For VLAN 20 (sales): --> Router(config-if)#: encapsulation dot1Q 20
For VLAN 30 (marketing): --> Router(config-if)#: encapsulation dot1Q 30
```

- Assign an IP address to each subinterface within the respective VLAN range:

```c
 For VLAN 10 (accounting):--> Router(config-if)# ip address 192.168.10.1 255.255.255.192
 VLAN 20 (sales): --> Router(config-if)# ip address 192.168.20.1 255.255.255.192
 VLAN 30 (marketing): --> Router(config-if)# ip address 192.168.30.1 255.255.255.192
 ```

- While in interface gigabitethernet0/0 enable the interface by typing `no shutdown` and this while enable the subinterfaces as well.

### Configuration of Switch:
- Click on the switch and select "CLI" to access its configuration mode.
- Create VLANs on the switch for sales, marketing, and accounting:
  
```c
Switch(config)# vlan 10
Switch(config-vlan)# name Sales
Switch(config)# vlan 20
Switch(config-vlan)# name Accounting
Switch(config)# vlan 30
Switch(config-vlan)# name Marketing
```

Assign switch ports to the appropriate VLAN using the interface and switchport access vlan commands.

```c
Switch(config)# interface range fastethernet0/1 - 3
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 10
```

Repeat the above steps for VLANs 20 (fastethernet0/4-6) and 30 (fastethernet0/7-9), assigning ports to the corresponding VLANs.

We will need to setup trunking on the switch inteface that is connected to the router and for this instance I select fastehter0/11 on the switch
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
  Computer 1: IP address: 192.168.1.2, Subnet mask: 255.255.255.192, Default gateway: 192.168.1.1
  Computer 2: IP address: 192.168.1.3, Subnet mask: 255.255.255.192, Default gateway: 192.168.1.1
  Computer 3: IP address: 192.168.1.4, Subnet mask: 255.255.255.192, Default gateway: 192.168.1.1
```
Repeat the above steps for VLANs 20 (start at 192.168.1.66)  and 30 (start at192.168.1.130), assigning appropriate IP addresses to computers in each VLAN.

```c
  Computer 4: IP address: 192.168.1.66, Subnet mask: 255.255.255.192, Default gateway: 192.168.1.65
  Computer 5: IP address: 192.168.1.67, Subnet mask: 255.255.255.192, Default gateway: 192.168.1.65
  Computer 6: IP address: 192.168.1.68, Subnet mask: 255.255.255.192, Default gateway: 192.168.1.65
```

  - <img width="500" alt="computer ip address" src="https://github.com/0xbythesecond/Setting_Up_VLAN/assets/23303634/a3d6a7fc-efa0-4ecb-9191-1868a8cf1fe6"/>


### Testing Connectivity Within VLANs:
- Test connectivity between computers within the same VLAN using the ping and outside of the VLAN command.
- Ensure that computers in the same VLAN can communicate with each other.
  - <img width="600" alt="ping different vlans" src="https://github.com/0xbythesecond/Setting_Up_VLAN/assets/23303634/aecf98f7-eb98-4264-8eea-b702309ad8cb"/>
 

### Verification of Inter-VLAN Routing:
- Use the show `ip route` command in router CLI to verify that the router has learned routes for each VLAN subnet.
  - <img width="600" alt="show ip route" src="https://github.com/0xbythesecond/Setting_Up_VLAN/assets/23303634/fc4bd991-c1ae-4fe6-8762-6d0b50b2dbdc"/>
 
<img width="800" alt="updated vlan topology" src="https://github.com/0xbythesecond/Setting_Up_VLAN/assets/23303634/bc24d8ce-8e99-4e10-9f0e-ad9005053aba"/>


### Conclusion
In conclusion, the setup of the network with multiple VLANs has been successfully completed, demonstrating efficient communication within each department represented by VLANs. Through VLAN segmentation, we've effectively organized network traffic and enhanced security by restricting communication between departments. Overall, this project has underscored the importance of VLANs in network design and management, paving the way for optimized communication and security within our organization.



