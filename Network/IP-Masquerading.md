# Inter VLAN NAT
Inter-VLAN communication allows devices on different VLANs (Virtual Local Area Networks) to communicate with each other. Normally, this is done using a Layer 3 device, like a router or a Layer 3 switch.  


|        Feature       |                        DNAT (Destination NAT)                        |                          SNAT (Source NAT)                          |
|:--------------------:|:--------------------------------------------------------------------:|:-------------------------------------------------------------------:|
| What it changes      | Destination IP of incoming packets                                   | Source IP of outgoing packets                                       |
| Use when…            | You want to fake the location of the server to the client.           | You want to fake the location of the client to a server.            |
| Visibility on server | Server still sees the real client IP as source (good for auditing).  | Server sees only the router IP (masks client identity).                 |
| Firewall granularity | Very tight: you control exactly which dst-IP+port get forwarded.     | Less tight: you must filter on src-IP = router, not individual clients. |
| Complexity           | Moderate: one PREROUTING rule, conntrack handles return.             | Simple: one POSTROUTING rule, but hides origin.                     |

Masquerading = Dynamic SNAT (for dynamic IPs)

| Scenario                                           | Use This     |
| -------------------------------------------------- | ------------ |
| Inter-VLAN with simple routing                     | No NAT       |
| Inter-VLAN but hide source IP                      | SNAT         |
| Inter-VLAN redirection                             | DNAT         |
| Dynamic outbound traffic from VLAN with unknown IP | Masquerading |

## Secure thinking
→ in most cases for security, DNAT wins, because you:
- Preserve client identity: HA logs the TV’s real IP.
- Lock down exactly which “fake” IP+port on the IoT VLAN gets mapped.
- Avoid broad source-hiding that might accidentally open up other traffic.
