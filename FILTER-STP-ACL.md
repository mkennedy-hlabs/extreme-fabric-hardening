# Spanning-Tree Filters

## Overview
01:80:c2:00:00:00 is the IEEE standard bridge-control destination MAC. IEEE identifies 01-80-C2-00-00-00 as the Bridge Group address / Nearest Customer Bridge group address.  This exact destination MAC is used by STP, RSTP, and MSTP BPDUs. IEEE further notes that frames sent to the BPDU block are not forwarded by an 802.1 bridge, which is why this address is used for link-local control traffic that should be processed by the adjacent bridge rather than relayed through the network.

01:00:0c:cc:cc:cd is the Cisco proprietary spanning-tree multicast destination MAC used for SSTP/PVST+/Rapid PVST+ control traffic. This Cisco Shared Spanning Tree Protocol (SSTP) multicast MAC is used to carry PVST+/Rapid PVST+ topology information. Cisco’s registered OUI 00-00-0C is assigned to Cisco Systems, and Cisco uses the multicast destination 01-00-0C-CC-CC-CD for this vendor-specific spanning-tree control plane

---
### **DROP-STP**
```
filter acl ;&ltAcl-id&gt; type inPort
filter acl port 1 1/1,1/2,1/4
filter acl ace ;&ltAcl-id&gt; ;&ltAcl-id&gt;
filter acl ace action 1 1 deny count
filter acl ace ethernet 1 1 dst-mac eq 01:00:0c:cc:cc:cd
filter acl ace 1 1 enable
filter acl ace 1 2
filter acl ace action 1 2 deny count
filter acl ace ethernet 1 2 dst-mac eq 01:80:c2:00:00:00
filter acl ace 1 2 enable

```
---
### **DROP-STP Example**
```
filter acl 1 type inPort
filter acl port 1 1/1,1/2,1/4
filter acl ace 1 1
filter acl ace action 1 1 deny count
filter acl ace ethernet 1 1 dst-mac eq 01:00:0c:cc:cc:cd
filter acl ace 1 1 enable
filter acl ace 1 2
filter acl ace action 1 2 deny count
filter acl ace ethernet 1 2 dst-mac eq 01:80:c2:00:00:00
filter acl ace 1 2 enable

```