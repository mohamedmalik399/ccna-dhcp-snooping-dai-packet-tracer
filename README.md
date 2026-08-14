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
           

 ## How DHCP Snooping and DAI Work Together
 
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
 - show interfaces trunk
 - show ip dhcp pool
 - show ip dhcp binding
 - show ip dhcp snooping
 - show ip dhcp snooping binding
 - show ip arp inspection
 - show ip arp inspection interfaces

Key CCNA Takeaways
 - DHCP Snooping ports toward legitimate DHCP servers are trusted.
 - End-host ports should normally remain untrusted.
 - DHCP Snooping creates a binding table containing client information.
 - DAI is enabled per VLAN with ip arp inspection vlan <vlan-id>.
 - DAI normally inspects ARP received on untrusted ports.
 - DAI can use DHCP Snooping bindings to validate ARP information.
 - Trust should be applied carefully because trusted ports bypass the intended inspection path.
