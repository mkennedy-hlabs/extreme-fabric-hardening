# Guest Filters

## Overview
The Guest filter is designed to limit Guest Access on the network.  Guest Access might be interpurted different between customers, so this basic filter allows DHCP and DNS, while blocking any private subnet ranges.  Essentailly, allowing onlyh access to only the Internet, but accomodating local DNS and DHCP services.  Again, the default global action on filters is to Permit.
If copying the configuration below, pay attention that the ACL ID is 1, this needs to be available on the switch.  If not, this needs to be changed to an available ACL number between 1-1999 (security ACLs).  

Depending on where the client is entering the network, a VLAN filter may also be required.  A typical, scenario where an accociated VLAN filter would be required is on a collapsed core solution.  In this scenario, the IP addressing for the Guest and the Guest clients are both local and will not traverse the Fabric.

Services:
- DHCP
- DNS
- NTP
- Internet

---
### **RESTRICT-GUEST-ISID**
```
filter acl 1 type inVsn matchType both name "RESTRICT-GUEST-ISID"
filter acl i-sid 1 <GUEST I_SID>

filter acl ace 1 5 name "PERMIT-DHCP_CLIENT_68_TO_67"
filter acl ace action 1 5 permit count
filter acl ace action 1 5 permit
filter acl ace ethernet 1 5 ether-type eq ip
filter acl ace ip 1 5 ip-protocol-type eq udp
filter acl ace protocol 1 5 src-port eq 68
filter acl ace protocol 1 5 dst-port eq bootpServer
filter acl ace 1 5 enable

filter acl ace 1 6 name "PERMIT-DHCP_SERVER_67_TO_68"
filter acl ace action 1 6 permit count
filter acl ace action 1 6 permit
filter acl ace ethernet 1 6 ether-type eq ip
filter acl ace ip 1 6 ip-protocol-type eq udp
filter acl ace protocol 1 6 src-port eq 67
filter acl ace protocol 1 6 dst-port eq bootpClient
filter acl ace 1 6 enable

filter acl ace 1 10 name "PERMIT-DNS_UDP_DST53"
filter acl ace action 1 10 permit count
filter acl ace action 1 10 permit
filter acl ace ethernet 1 10 ether-type eq ip
filter acl ace ip 1 10 ip-protocol-type eq udp
filter acl ace protocol 1 10 dst-port eq dns
filter acl ace 1 10 enable

filter acl ace 1 11 name "PERMIT-DNS_UDP_SRC53"
filter acl ace action 1 11 permit count
filter acl ace action 1 11 permit
filter acl ace ethernet 1 11 ether-type eq ip
filter acl ace ip 1 11 ip-protocol-type eq udp
filter acl ace protocol 1 11 src-port eq 53
filter acl ace 1 11 enable

filter acl ace 1 12 name "PERMIT-DNS_TCP_DST53"
filter acl ace action 1 12 permit count
filter acl ace action 1 12 permit
filter acl ace ethernet 1 12 ether-type eq ip
filter acl ace ip 1 12 ip-protocol-type eq tcp
filter acl ace protocol 1 12 dst-port eq dns
filter acl ace 1 12 enable

filter acl ace 1 13 name "PERMIT-DNS_TCP_SRC53"
filter acl ace action 1 13 permit count
filter acl ace action 1 13 permit
filter acl ace ethernet 1 13 ether-type eq ip
filter acl ace ip 1 13 ip-protocol-type eq tcp
filter acl ace protocol 1 13 src-port eq 53
filter acl ace 1 13 enable

filter acl ace 1 60 name "DENY-RFC1918-10/8"
filter acl ace action 1 60 deny count
filter acl ace ethernet 1 60 ether-type eq ip
filter acl ace ip 1 60 dst-ip 10.0.0.0 255.0.0.0
filter acl ace 1 60 enable

filter acl ace 1 70 name "DENY-RFC1918-172.16/12"
filter acl ace action 1 70 deny count
filter acl ace ethernet 1 70 ether-type eq ip
filter acl ace ip 1 70 dst-ip 172.16.0.0 255.240.0.0
filter acl ace 1 70 enable

filter acl ace 1 80 name "DENY-RFC1918-192.168/16"
filter acl ace action 1 80 deny count
filter acl ace ethernet 1 80 ether-type eq ip
filter acl ace ip 1 80 dst-ip 192.168.0.0 255.255.0.0
filter acl ace 1 80 enable
```
### **RESTRICT-GUEST-VLAN**
```
filter acl 2 type inVlan name "RESTRICT-GUEST-VLAN"
filter acl vlan 2 <GUEST VLAN>

filter acl ace 2 5 name "PERMIT-DHCP_CLIENT_68_TO_67"
filter acl ace action 2 5 permit count
filter acl ace action 2 5 permit
filter acl ace ethernet 2 5 ether-type eq ip
filter acl ace ip 2 5 ip-protocol-type eq udp
filter acl ace protocol 2 5 src-port eq 68
filter acl ace protocol 2 5 dst-port eq bootpServer
filter acl ace 2 5 enable

filter acl ace 2 6 name "PERMIT-DHCP_SERVER_67_TO_68"
filter acl ace action 2 6 permit count
filter acl ace action 2 6 permit
filter acl ace ethernet 2 6 ether-type eq ip
filter acl ace ip 2 6 ip-protocol-type eq udp
filter acl ace protocol 2 6 src-port eq 67
filter acl ace protocol 2 6 dst-port eq bootpClient
filter acl ace 2 6 enable

filter acl ace 2 10 name "PERMIT-DNS_UDP_DST53"
filter acl ace action 2 10 permit count
filter acl ace action 2 10 permit
filter acl ace ethernet 2 10 ether-type eq ip
filter acl ace ip 2 10 ip-protocol-type eq udp
filter acl ace protocol 2 10 dst-port eq dns
filter acl ace 2 10 enable

filter acl ace 2 11 name "PERMIT-DNS_UDP_SRC53"
filter acl ace action 2 11 permit count
filter acl ace action 2 11 permit
filter acl ace ethernet 2 11 ether-type eq ip
filter acl ace ip 2 11 ip-protocol-type eq udp
filter acl ace protocol 2 11 src-port eq 53
filter acl ace 2 11 enable

filter acl ace 2 12 name "PERMIT-DNS_TCP_DST53"
filter acl ace action 2 12 permit count
filter acl ace action 2 12 permit
filter acl ace ethernet 2 12 ether-type eq ip
filter acl ace ip 2 12 ip-protocol-type eq tcp
filter acl ace protocol 2 12 dst-port eq dns
filter acl ace 2 12 enable

filter acl ace 2 13 name "PERMIT-DNS_TCP_SRC53"
filter acl ace action 2 13 permit count
filter acl ace action 2 13 permit
filter acl ace ethernet 2 13 ether-type eq ip
filter acl ace ip 2 13 ip-protocol-type eq tcp
filter acl ace protocol 2 13 src-port eq 53
filter acl ace 2 13 enable

filter acl ace 2 60 name "DENY-RFC1918-10/8"
filter acl ace action 2 60 deny count
filter acl ace ethernet 2 60 ether-type eq ip
filter acl ace ip 2 60 dst-ip 10.0.0.0 255.0.0.0
filter acl ace 2 60 enable

filter acl ace 2 70 name "DENY-RFC1918-172.16/12"
filter acl ace action 2 70 deny count
filter acl ace ethernet 2 70 ether-type eq ip
filter acl ace ip 2 70 dst-ip 172.16.0.0 255.240.0.0
filter acl ace 2 70 enable

filter acl ace 2 80 name "DENY-RFC1918-192.168/16"
filter acl ace action 2 80 deny count
filter acl ace ethernet 2 80 ether-type eq ip
filter acl ace ip 2 80 dst-ip 192.168.0.0 255.255.0.0
filter acl ace 2 80 enable
```
