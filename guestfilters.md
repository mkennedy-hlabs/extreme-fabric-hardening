# Guest Filters

## Overview
The Guest filter is designed to limit Guest Access on the network.  Guest Access might be interpurted different between customers, so this basic filter allows DHCP and DNS, while blocking any private subnet ranges.  Essentailly, allowing onlyh access to only the Internet, but accomodating local DNS and DHCP services.  Again, the default global action on filters is to Permit.
If copying the configuration below, pay attention that the ACL ID is 1, this needs to be available on the switch.  If not, this needs to be changed to an available ACL number between 1-1999 (security ACLs).  Additionally, the guest VLAN and I-SID details are defined as variable placeholders, to be edited as required.

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

filter acl ace 1 10 name "PERMIT-DNS_UDP_DST53"
filter acl ace action 1 10 permit count
filter acl ace action 1 10 permit
filter acl ace ethernet 1 10 ether-type eq ip
filter acl ace ip 1 10 ip-protocol-type eq udp
filter acl ace protocol 1 10 dst-port eq dns
filter acl ace 1 10 enable

filter acl ace 1 11 name "PERMIT-DNS_TCP_DST53"
filter acl ace action 1 11 permit count
filter acl ace action 1 11 permit
filter acl ace ethernet 1 11 ether-type eq ip
filter acl ace ip 1 11 ip-protocol-type eq tcp
filter acl ace protocol 1 11 dst-port eq dns
filter acl ace 1 11 enable

filter acl ace 1 80 name "DENY-RFC1918-10/8"
filter acl ace action 1 80 deny count
filter acl ace ethernet 1 80 ether-type eq ip
filter acl ace ip 1 80 dst-ip mask 10.0.0.0 8
filter acl ace 1 80 enable

filter acl ace 1 90 name "DENY-RFC1918-172.16/12"
filter acl ace action 1 90 deny count
filter acl ace ethernet 1 90 ether-type eq ip
filter acl ace ip 1 90 dst-ip mask 172.16.0.0 12
filter acl ace 1 90 enable

filter acl ace 1 100 name "DENY-RFC1918-192.168/16"
filter acl ace action 1 100 deny count
filter acl ace ethernet 1 100 ether-type eq ip
filter acl ace ip 1 100 dst-ip mask 192.168.0.0 16
filter acl ace 1 100 enable
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

filter acl ace 2 10 name "PERMIT-DNS_UDP_DST53"
filter acl ace action 2 10 permit count
filter acl ace action 2 10 permit
filter acl ace ethernet 2 10 ether-type eq ip
filter acl ace ip 2 10 ip-protocol-type eq udp
filter acl ace protocol 2 10 dst-port eq dns
filter acl ace 2 10 enable

filter acl ace 2 11 name "PERMIT-DNS_TCP_DST53"
filter acl ace action 2 11 permit count
filter acl ace action 2 11 permit
filter acl ace ethernet 2 11 ether-type eq ip
filter acl ace ip 2 11 ip-protocol-type eq tcp
filter acl ace protocol 2 11 dst-port eq dns
filter acl ace 2 11 enable

filter acl ace 2 80 name "DENY-RFC1918-10/8"
filter acl ace action 2 80 deny count
filter acl ace ethernet 2 80 ether-type eq ip
filter acl ace ip 2 80 dst-ip mask 10.0.0.0 8
filter acl ace 2 80 enable

filter acl ace 2 90 name "DENY-RFC1918-172.16/12"
filter acl ace action 2 90 deny count
filter acl ace ethernet 2 90 ether-type eq ip
filter acl ace ip 2 90 dst-ip mask 172.16.0.0 12
filter acl ace 2 90 enable

filter acl ace 2 100 name "DENY-RFC1918-192.168/16"
filter acl ace action 2 100 deny count
filter acl ace ethernet 2 100 ether-type eq ip
filter acl ace ip 2 100 dst-ip mask 192.168.0.0 16
filter acl ace 2 100 enable
```
