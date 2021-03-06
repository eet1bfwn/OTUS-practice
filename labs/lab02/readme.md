# Лабораторная работа "VLAN и маршрутизация между VLAN"

Топология:

![](screenshots/2021-03-06-18-27-22-image.png)

 Адресация:

| Device | Interface | IP Address   | Subnet Mask   | Default Gateway |
| ------ | --------- | ------------ | ------------- | --------------- |
| R1     | G0/0/1.3  | 192.168.3.1  | 255.255.255.0 | N/A             |
| R1     | G0/0/1.4  | 192.168.4.1  | 255.255.255.0 | N/A             |
| R1     | G0/0/1.8  | N/A          | N/A           | N/A             |
| S1     | VLAN 3    | 192.168.3.11 | 255.255.255.0 | 192.168.3.1     |
| S2     | VLAN 3    | 192.168.3.12 | 255.255.255.0 | 192.168.3.1     |
| PC-A   | NIC       | 192.168.3.3  | 255.255.255.0 | 192.168.3.1     |
| PC-B   | NIC       | 192.168.4.3  | 255.255.255.0 | 192.168.4.1     |

VLAN 

| VLAN | Name       | Interface Assigned                                        |
| ---- | ---------- | --------------------------------------------------------- |
| 3    | Management | S1: VLAN 3 S2: VLAN 3 S1: F0/6                            |
| 4    | Operations | S2: F0/18                                                 |
| 7    | ParkingLot | S1: F0/2-4, F0/7-24, G0/1-2 S2: F0/2-17, F0/19-24, G0/1-2 |
| 8    | Native     | N/A                                                       |



# Objectives

[Part 1: Build the Network and Configure Basic Device Settings](lab02#Part 1: Build the Network and Configure Basic Device Settings)

Part 2: Create VLANs and Assign Switch Ports

Part 3: Configure an 802.1Q Trunk between the Switches

Part 4: Configure Inter-VLAN Routing on the Router

Part 5: Verify Inter-VLAN Routing is working



# Part 1: Build the Network and Configure Basic Device Settings
