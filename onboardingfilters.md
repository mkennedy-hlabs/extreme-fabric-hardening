# Onboarding Filters

## Overview
Virtual Router Redundancy Protocol (VRRP) (RFC 2338) provides another layer of resiliency to the network design by providing default gateway redundancy for end
users. VRRP eliminates the single point of failure that can occur when the single static default gateway router for an end station is lost. A loss of the default gateway router
causes a loss of connectivity to the remote networks. If a VRRP-enabled router that connects to the default gateway fails, failover to the VRRP backup router ensures no
interruption for end users when attempting to route from their local subnet.

VRRP shares a virtual IP address (transparent to users) between two or more routers that connect the common subnet to the enterprise network. With the virtual IP
address as the default gateway on end hosts, VRRP provides dynamic default gateway redundancy in the event of failover.

The VRRP router that controls the IP addresses associated with a virtual router is the primary router and it forwards packets to these IP addresses. The election
process provides a dynamic transition of forwarding responsibility if the primary router becomes unavailable. (The primary router is sometimes referred to as the master.)

When a VRRP router is initialized it sends a VRRP advertisement. The VRRP router also broadcasts a gratuitous ARP request that contains the virtual router MAC address for
each IP address associated with the virtual router. The VRRP router then transitions to the controlling state.

In the controlling state, the VRRP router functions as the forwarding router for the IP addresses associated with the virtual router. The VRRP router responds to ARP requests
for these IP addresses, forwards packets with a destination MAC address equal to the virtual router MAC address, and accepts packets addressed to IP addresses associated
with the virtual router.

In the backup state, a VRRP router monitors the availability and state of the primary router. The backup router does not respond to ARP requests and must discard packets
with a MAC address equal to the virtual router MAC address. The backup router does not accept packets addressed to IP addresses associated with the virtual router. If
a shutdown occurs, the backup router transitions back to the initialize state. If the primary router goes down, the backup router sends the VRRP advertisement and ARP
request described in the preceding paragraph and transitions to the controlling state.

The router transitions to the backup state in the following situations:<br>
• If the priority is greater than the local priority<br>
• If the priority is the same as the local priority and the primary IP address of the sender is greater than the local primary IP address<br>

Otherwise, the router discards the advertisement. If a shutdown occurs, the primary router sends a VRRP advertisement with a priority of 0 and transitions to the initialize
state.

---
![AnyCastGateway](./diagrams/anycast_gw.png)

## 1. **VRRP**
This example will configure 2 switches with the VRRP IP Gateway configuration.  Core 1 and Core 2 will have unique configured IP addresses, along with a similar default VRRP Gateway IP address.  In this scenario, these 2 switches are in an vIST cluster; however this is not a requirement for VRRP IP Gateway.<br>

This is the most basic configuration of the VRRP protocol.  In this scenario only the master will forward IP traffic, while the back-up is in standby mode ready to forward if there is a failure of the master.  When choosing a primary in this scenario both have the default priority of 100 set, the tiebreak for master is the switch with the highest IP address. In this case Core 2. 

### **Core 1**
```
config t
vlan create 1630 name "VRRP_DATA" type port-mstprstp 0
vlan i-sid 1630 101630
interface Vlan 1630
ip address 172.16.30.2 255.255.255.0
ip vrrp version 2
ip vrrp address 1 172.16.30.1
ip vrrp 1 enable
exit
```

### **Core 2**
```
config t
vlan create 1630 name "VRRP_DATA" type port-mstprstp 0
vlan i-sid 1630 101630
interface Vlan 1630
ip address 172.16.30.3 255.255.255.0
ip vrrp version 2
ip vrrp address 1 172.16.30.1
ip vrrp 1 enable
exit
```
- A VRID is required which can be unqiue or the same for each VLAN within the systems.  In the above example a VRID of 1 was defined.  This is typically the default set on most systems and in most cases can be used without issue or concern.  However, there are cases where using 1 might interfere with VRRP configurations on other systems (e.g. firewalls).  So when in doubt, use a VRID of something other than 1.
<pre>
ip vrrp address &lt;vrid 1-255&gt; 172.16.30.1
</pre>

  ---

## 2. **Verification**
To display an entry for each local routing interface use the command **_show ip anycast-gateway interface_**.
- show ip anycast-gateway interfaces **[i-sid <1-15999999>] [vlan
<1-4059>] [vrf WORD<1-16>] [vrfids WORD<0-512>]**

<pre style="margin:0;">#show ip anycast-gateway interfaces
========================================================================================================================
                                                   Anycast interfaces
========================================================================================================================
                                                              ADMIN       OPER              VRF     VRF
L2VSN       VLAN GW IPv4          GW MAC             VRID     STATUS      STATE     ONE-IP  ID      Name
------------------------------------------------------------------------------------------------------------------------
101601      1601 172.16.1.1       00:00:5e:00:01:27  39       enable      up        false   0       GlobalRouter
101603      1603 172.16.3.1       00:00:5e:00:01:27  39       enable      up        false   0       GlobalRouter
101604      1604 172.16.4.1       00:00:5e:00:01:27  39       enable      up        false   0       GlobalRouter
101609      1609 172.16.9.1       00:00:5e:00:01:28  40       enable      up        false   0       GlobalRouter
</pre>
<br>

To display routing information for Anycast IP Gateway interfaces on all BEBs use the command **_show ip anycast-gateway i-sid_**.
- show ip anycast-gateway i-sid **[<1-15999999>] [adv-sys-id
<xxxx.xxxx.xxxx>] [all-routers] [home] [remote]**<br>

This example displays the routing information on the switch without any optional command parameters

<pre style="margin:0;">#show ip anycast-gateway i-sid
==================================================================================================================================
                                                               Anycast I-SID
==================================================================================================================================
                                                    PATH       ADD
L2VSN    L3VSN    GW IPv4         GW MAC            COST       METRIC     NEXT-HOP-BMAC     AREA   ADV-SYS-ID     ADV-RTR
----------------------------------------------------------------------------------------------------------------------------------
101601   0        172.16.1.1      00:00:5e:00:01:27 0          0          02:0c:54:20:01:00 HOME   020c.5420.0100 CORE-1
101603   0        172.16.3.1      00:00:5e:00:01:27 0          0          02:0c:54:20:01:00 HOME   020c.5420.0100 CORE-1
101604   0        172.16.4.1      00:00:5e:00:01:27 0          0          02:0c:54:20:01:00 HOME   020c.5420.0100 CORE-1
</pre>

While the following example displays information for all advertising routers.
- The Active column displays if the advertising node is the installed Anycast IP Gateway router.  Typically in a two router configuration the other router would produce an opposite display.<br>

### **Core 1**
<pre style="margin:0;">#show ip anycast-gateway i-sid all-routers
===================================================================================================================================
                                                               Anycast I-SID
===================================================================================================================================
                                                    PATH       ADD
L2VSN    L3VSN    GW IPv4         GW MAC            COST       METRIC     NEXT-HOP-BMAC     AREA   ADV-SYS-ID     ACTIVE ADV-RTR
-----------------------------------------------------------------------------------------------------------------------------------
101601   0        172.16.1.1      00:00:5e:00:01:27 0          0          02:0c:54:20:01:00 HOME   020c.5420.0100 true   CORE-1
101601   0        172.16.1.1      00:00:5e:00:01:27 2000       0          02:0c:54:20:02:00 HOME   020c.5420.0200 false  CORE-2
101603   0        172.16.3.1      00:00:5e:00:01:27 0          0          02:0c:54:20:01:00 HOME   020c.5420.0100 true   CORE-1
101603   0        172.16.3.1      00:00:5e:00:01:27 2000       0          02:0c:54:20:02:00 HOME   020c.5420.0200 false  CORE-2
101604   0        172.16.4.1      00:00:5e:00:01:27 0          0          02:0c:54:20:01:00 HOME   020c.5420.0100 true   CORE-1
101604   0        172.16.4.1      00:00:5e:00:01:27 2000       0          02:0c:54:20:02:00 HOME   020c.5420.0200 false  CORE-2
</pre>

### **Core 2**
<pre style="margin:0;">#show ip anycast-gateway i-sid all-routers
===================================================================================================================================
                                                               Anycast I-SID
===================================================================================================================================
                                                    PATH       ADD
L2VSN    L3VSN    GW IPv4         GW MAC            COST       METRIC     NEXT-HOP-BMAC     AREA   ADV-SYS-ID     ACTIVE ADV-RTR
-----------------------------------------------------------------------------------------------------------------------------------
101601   0        172.16.1.1      00:00:5e:00:01:27 0          0          02:0c:54:20:01:00 HOME   020c.5420.0100 false  CORE-1
101601   0        172.16.1.1      00:00:5e:00:01:27 2000       0          02:0c:54:20:02:00 HOME   020c.5420.0200 true   CORE-2
101603   0        172.16.3.1      00:00:5e:00:01:27 0          0          02:0c:54:20:01:00 HOME   020c.5420.0100 false  CORE-1
101603   0        172.16.3.1      00:00:5e:00:01:27 2000       0          02:0c:54:20:02:00 HOME   020c.5420.0200 true   CORE-2
101604   0        172.16.4.1      00:00:5e:00:01:27 0          0          02:0c:54:20:01:00 HOME   020c.5420.0100 false  CORE-1
101604   0        172.16.4.1      00:00:5e:00:01:27 2000       0          02:0c:54:20:02:00 HOME   020c.5420.0200 true   CORE-2
</pre>

---

## 3. **Edge Verification**
The **_show ip anycast-gateway interface_** is not relevant on a L2VSN BEB as it does not contain any IP addressing.  The **_show ip anycast-gateway i-sid_** will be the primary command used for varification at the L2 edge. 
- The **_show ip anycast-gateway i-sid_** without any additional parameters displays the current active shortest path to the gateway.  As indicated in the output below odd I-SIDs create a shortest path to the higher system-ID, while even I-SIDs create a shortest path to the switch with the lower system-id.
- Additionally, the next-hop-bmac is adverstised as the smlt-virtual-bmac in the scenario where the routers are configured as a vIST cluster.

<pre style="margin:0;">#show ip anycast-gateway i-sid
==================================================================================================================================
                                                               Anycast I-SID
==================================================================================================================================
                                                    PATH       ADD
L2VSN    L3VSN    GW IPv4         GW MAC            COST       METRIC     NEXT-HOP-BMAC     AREA   ADV-SYS-ID     ADV-RTR
----------------------------------------------------------------------------------------------------------------------------------
101601   0        172.16.1.1      00:00:5e:00:01:27 2000       0          02:0c:54:20:01:ff HOME   020c.5420.0100 CORE-1
101603   0        172.16.3.1      00:00:5e:00:01:27 2000       0          02:0c:54:20:01:ff HOME   020c.5420.0100 CORE-1
101604   0        172.16.4.1      00:00:5e:00:01:27 2000       0          02:0c:54:20:01:ff HOME   020c.5420.0200 CORE-2
</pre>

- If the all-routers is appended to the command a list of all the advertising routers and thier path costs are listed, along with which is currently active for the I-SID.
<pre style="margin:0;">#show ip anycast-gateway i-sid all-routers
************************************************************************************
                Command Execution Time: Sun Mar 01 11:14:04 2026 CST
************************************************************************************

===================================================================================================================================
                                                               Anycast I-SID
===================================================================================================================================
                                                    PATH       ADD
L2VSN    L3VSN    GW IPv4         GW MAC            COST       METRIC     NEXT-HOP-BMAC     AREA   ADV-SYS-ID     ACTIVE ADV-RTR
-----------------------------------------------------------------------------------------------------------------------------------
101601   0        172.16.1.1      00:00:5e:00:01:27 2000       0          02:0c:54:20:01:ff HOME   020c.5420.0100 true   CORE-1
101601   0        172.16.1.1      00:00:5e:00:01:27 2000       0          02:0c:54:20:01:ff HOME   020c.5420.0200 false  CORE-2
101603   0        172.16.3.1      00:00:5e:00:01:27 2000       0          02:0c:54:20:01:ff HOME   020c.5420.0100 true   CORE-1
101603   0        172.16.3.1      00:00:5e:00:01:27 2000       0          02:0c:54:20:01:ff HOME   020c.5420.0200 false  CORE-2
101604   0        172.16.4.1      00:00:5e:00:01:27 2000       0          02:0c:54:20:01:ff HOME   020c.5420.0100 false  CORE-1
101604   0        172.16.4.1      00:00:5e:00:01:27 2000       0          02:0c:54:20:01:ff HOME   020c.5420.0200 true   CORE-2
</pre>
---

