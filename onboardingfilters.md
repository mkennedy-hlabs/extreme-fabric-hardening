# Onboarding Filters

## Overview
The Onboarding I-SID is designed to be used during the ZTP+ process, hense this filter is used limit network exposeure to only allow required services for the ZTP+ process to function.  When a default switch is connected to the network the it will use the Onbaording VLAN/I-SID to obatin an IP address from DHCP, then perform a DNS lookup to extremecontrol.&lt<local domain>&gt so it can communicate with XIQ-SE and start the ZTP+ process. 
The default global operation of the filter is permit, so a Deny Any is included at the end.

If copying the configuration below, pay attention that the ACL ID is 1, this needs to be available on the switch.  If not, this needs to be changed to an available ACL number between 1-1999 (security ACLs).  Additionally, there is no IP address defined for the XIQ-SE only a variable place holder &lt<XIQ-SE IP ADDRESS>&gt this needs to be updated for the local XIQ-SE instance.

Services:
- DHCP
- DNS
- NTP
- XIQ-SE
- ICMP (required becuase ICMP is used to validate DNS servers are reachable)

---
### **RESTRICT-ONBOARDING-ISID**
```
filter acl 1 type inVsn matchType both name "RESTRICT-ONBOARDING-ISID"
filter acl i-sid 1 15999999

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

filter acl ace 1 20 name "PERMIT-NTP_DST123"
filter acl ace action 1 20 permit count
filter acl ace action 1 20 permit
filter acl ace ethernet 1 20 ether-type eq ip
filter acl ace ip 1 20 ip-protocol-type eq udp
filter acl ace protocol 1 20 dst-port eq 123
filter acl ace 1 20 enable

filter acl ace 1 21 name "PERMIT-NTP_SRC123"
filter acl ace action 1 21 permit count
filter acl ace action 1 21 permit
filter acl ace ethernet 1 21 ether-type eq ip
filter acl ace ip 1 21 ip-protocol-type eq udp
filter acl ace protocol 1 21 src-port eq 123
filter acl ace 1 21 enable

filter acl ace 1 30 name "PERMIT-ICMP"
filter acl ace action 1 30 permit count
filter acl ace action 1 30 permit
filter acl ace ethernet 1 30 ether-type eq ip
filter acl ace ip 1 30 ip-protocol-type eq icmp
filter acl ace 1 30 enable

filter acl ace 1 90 name "PERMIT-XIQ-SE_SRC"
filter acl ace action 1 90 permit count
filter acl ace action 1 90 permit
filter acl ace ethernet 1 90 ether-type eq ip
filter acl ace ip 1 90 src-ip eq <XIQ-SE IP ADDRESS>
filter acl ace 1 90 enable

filter acl ace 1 91 name "PERMIT-XIQ-SE_DST"
filter acl ace action 1 91 permit count
filter acl ace action 1 91 permit
filter acl ace ethernet 1 91 ether-type eq ip
filter acl ace ip 1 91 dst-ip eq  <XIQ-SE IP ADDRESS>
filter acl ace 1 91 enable

filter acl ace 1 100 name "DENY-ALL-IPV4-ELSE"
filter acl ace action 1 100 deny count
filter acl ace action 1 100 deny
filter acl ace ethernet 1 100 ether-type eq ip
filter acl ace 1 100 enable
```
