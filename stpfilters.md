# Spanning-Tree Filters

## Overview
01:80:c2:00:00:00 is the IEEE standard bridge-control destination MAC. IEEE identifies 01-80-C2-00-00-00 as the Bridge Group address / Nearest Customer Bridge group address.  This exact destination MAC is used by STP, RSTP, and MSTP BPDUs. IEEE further notes that frames sent to the BPDU block are not forwarded by an 802.1 bridge, which is why this address is used for link-local control traffic that should be processed by the adjacent bridge rather than relayed through the network.

01:00:0c:cc:cc:cd is the Cisco proprietary spanning-tree multicast destination MAC used for SSTP/PVST+/Rapid PVST+ control traffic. This Cisco Shared Spanning Tree Protocol (SSTP) multicast MAC is used to carry PVST+/Rapid PVST+ topology information. Cisco’s registered OUI 00-00-0C is assigned to Cisco Systems, and Cisco uses the multicast destination 01-00-0C-CC-CC-CD for this vendor-specific spanning-tree control plane

### **DROP-STP**

<pre style="margin:0;">
filter acl &lt;Acl-id&gt; type inPort
filter acl port &lt;Acl-id&gt; &lt;Port-List&gt;
filter acl ace &lt;Acl-id&gt &ltAce-id #1&gt;
filter acl ace action &lt;Acl-id&gt &ltAce-id #1&gt; deny count
filter acl ace ethernet &lt;Acl-id&gt  &ltAce-id #1&gt; dst-mac eq 01:00:0c:cc:cc:cd
filter acl ace &lt;Acl-id&gt &ltAce-id #1&gt; enable
filter acl ace &lt;Acl-id&gt &ltAce-id #2&gt;
filter acl ace action &lt;Acl-id&gt &ltAce-id #2&gt; deny count
filter acl ace ethernet &lt;Acl-id&gt &ltAce-id #2&gt; dst-mac eq 01:80:c2:00:00:00
filter acl ace &lt;Acl-id&gt &ltAce-id #2&gt; enable
</pre>

### **DROP-STP Example**
Within this example ACL 1 was chosen because it was available and not currently in use on the system. The interfaces connected to third-party STP devices are ports 1/1 through 1/4, though the number of affected ports may vary from one deployment to another. Each ACE represents an individual filter entry within the ACL and is processed sequentially. For example, the first entry is ACE 1 and the second entry is ACE 2, although ACE numbering can use any value between 1 and 2000. Since the default global filter action is permit, a deny action was explicitly configured for the targeted traffic, while all other traffic remains permitted. The count option was added only to confirm whether packets are matching the rule set 
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
