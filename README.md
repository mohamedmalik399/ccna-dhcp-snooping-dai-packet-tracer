# DHCP Snooping & Dynamic ARP Inspection (DAI) — Cisco Packet Tracer

A CCNA 200-301 security lab built in Cisco Packet Tracer to practice DHCP Snooping, the DHCP Snooping Binding Table, trusted/untrusted ports, DHCP Option 82 troubleshooting, and Dynamic ARP Inspection (DAI).

## Lab Objectives

- Configure a Cisco router as a DHCP server for VLAN 10
- Configure DHCP Snooping on VLAN 10
- Configure trusted and untrusted DHCP Snooping ports
- Verify the DHCP Snooping Binding Table
- Understand DHCP Snooping Option 82
- Troubleshoot DHCP when Option 82 is enabled
- Configure Dynamic ARP Inspection (DAI)
- Configure DAI trusted and untrusted interfaces
- Verify DAI operation and statistics
- Understand how DHCP Snooping and DAI work together

## Topology
                         R1
                  DHCP Server / Gateway
                     192.168.1.1
                          |
                     Fa0/3 TRUSTED
                          |
                         SW1
                    VLAN 10 (IT)
                  /       |       \
             Fa0/1     Fa0/2     Fa0/4
                |          |          |
               PC1        PC2        PC3
          192.168.1.11 .12       .13
           UNTRUSTED  UNTRUSTED  UNTRUSTED
           
IP Addressing:           
| Device     | IP Address     | VLAN | Switch Port |
| ---------- | -------------- | ---: | ----------- |
| R1 G0/0.10 | 192.168.1.1/24 |   10 | Fa0/3       |
| PC1        | 192.168.1.11   |   10 | Fa0/1       |
| PC2        | 192.168.1.12   |   10 | Fa0/2       |
| PC3        | 192.168.1.13   |   10 | Fa0/4       |

R1 DHCP Configuration :
ip dhcp excluded-address 192.168.1.2 192.168.1.10
!
ip dhcp pool POOL1
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 dns-server 8.8.8.8
!
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.1.1 255.255.255.0
 
DHCP Snooping Configuration:
 ip dhcp snooping
ip dhcp snooping vlan 10
!
interface FastEthernet0/3
 ip dhcp snooping trust
 
The client-facing interfaces remain untrusted.
Fa0/1 → Untrusted
Fa0/2 → Untrusted
Fa0/3 → Trusted
Fa0/4 → Untrusted

The binding table contains the learned relationship between the clients' MAC addresses, IP addresses, VLAN, and switch interfaces.

DHCP Option 82 Troubleshooting

During the lab, DHCP initially failed after DHCP Snooping was configured.

The switch was inserting DHCP Option 82 information.

In this Packet Tracer environment, disabling Option 82 insertion restored DHCP operation while DHCP Snooping remained enabled:
no ip dhcp snooping information option

This was a troubleshooting finding specific to the lab environment. Option 82 is an additional DHCP Snooping feature and is not inherently bad.

Dynamic ARP Inspection
DAI was enabled for VLAN 10:
ip arp inspection vlan 10

DAI interface trust configuration:
Fa0/1 → Untrusted
Fa0/2 → Untrusted
Fa0/3 → Trusted
Fa0/4 → Untrusted

DAI Verification
DAI was verified using:
show ip arp inspection

The switch reported:
Vlan     Configuration    Operation
10       Enabled          Active

Interface trust states were verified using:
show ip arp inspection interfaces

How DHCP Snooping and DAI Work Together
              DHCP Snooping
                    |
                    v
        DHCP Snooping Binding Table
             MAC + IP + VLAN
                    |
                    v
        Dynamic ARP Inspection
                    |
                    v
       Validate ARP on untrusted ports
                    |
             +------+------+
             |             |
           MATCH       NO MATCH
             |             |
             v             v
         FORWARD         DROP

DHCP Snooping
DHCP Snooping helps protect against rogue DHCP servers and builds the DHCP Snooping Binding Table.
Dynamic ARP Inspection
DAI uses the binding information to validate ARP messages received on untrusted interfaces.
Verification Commands:
show vlan brief
show interfaces trunk
show ip dhcp pool
show ip dhcp binding
show ip dhcp snooping
show ip dhcp snooping binding
show ip arp inspection
show ip arp inspection interfaces

Key CCNA Takeaways
DHCP Snooping ports toward legitimate DHCP servers are trusted.
End-host ports should normally remain untrusted.
DHCP Snooping creates a binding table containing client information.
DAI is enabled per VLAN with ip arp inspection vlan <vlan-id>.
DAI normally inspects ARP received on untrusted ports.
DAI can use DHCP Snooping bindings to validate ARP information.
Trust should be applied carefully because trusted ports bypass the intended inspection path.

SW1#show ip dhcp snooping binding
MacAddress          IpAddress        Lease(sec)  Type           VLAN  Interface
------------------  ---------------  ----------  -------------  ----  -----------------
00:0B:BE:56:2D:77   192.168.1.11     0           dhcp-snooping  10    FastEthernet0/1
00:60:47:79:49:9A   192.168.1.12     0           dhcp-snooping  10    FastEthernet0/2
00:E0:F9:97:93:4A   192.168.1.13     0           dhcp-snooping  10    FastEthernet0/4
Total number of bindings: 3

SW1#show ip arp inspection interfaces
Interface        Trust State     Rate(pps)    Burst Interval
---------------  -----------     ---------    --------------
Fa0/1            Untrusted              15                 1
Fa0/2            Untrusted              15                 1
Fa0/3            Trusted                15                 1
Fa0/4            Untrusted              15                 1

SW1#show ip arp inspection
Vlan     Configuration    Operation
----     -------------    ---------
10       Enabled          Active
