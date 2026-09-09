# wireshark-network-traffic-analysis
Hands-on network traffic analysis and security investigation using Wireshark in an isolated VirtualBox lab. Analyzes ARP, ICMP, DNS, TCP, HTTP, and Nmap-generated traffic to identify network behavior, communication patterns, and potential reconnaissance activity.

# Setup

                         Internet
                            │
                       NAT Adapter
                            │
             ┌──────────────┴──────────────┐
             │                             │
        Kali Linux VM                 Windows 10 VM
        ┌──────────────┐             ┌──────────────┐
        │              │             │              │
        │  Wireshark   │             │   Target     │
        │  Nmap        │             │   System     │
        │  tcpdump     │             │              │
        │              │             │              │
        └──────┬───────┘             └──────┬───────┘
               │                            │
               └──── Host-Only Network ─────┘
                       192.168.x.x

# Methodology
## Phase 1 : ICMP Analysis
### Traffic Generation
ICMP traffic was generated using the ping utility from Kali Linux.
### Observation
The capture contained ICMP Echo Request and Echo Reply packets between the Kali and Windows virtual machines.
### Inference
ICMP can be used for legitimate connectivity testing and network diagnostics. It can also provide useful information during network reconnaissance by identifying responsive hosts.
### Output obtained
<img width="958" height="503" alt="image" src="https://github.com/user-attachments/assets/49f42be1-9c77-4676-a024-577fb0363f58" />

## Phase 2 : ARP Analysis
Successfully captured the ARP exchange between Kali VM and Windows VM.
| Packet | ARP message                                     | Meaning                                   |
| ------ | ----------------------------------------------- | ----------------------------------------- |
| 11     | `Who has 192.168.213.101? Tell 192.168.213.102` | Windows asks: "Who owns 192.168.213.101?" |
| 12     | `192.168.213.101 is at 08:00:27:01:03:3a`       | Kali responds with its MAC address        |
| 14     | `Who has 192.168.213.102? Tell 192.168.213.101` | Kali asks: "Who owns 192.168.213.102?"    |
| 16     | `192.168.213.102 is at 08:00:27:94:c0:d9`       | Windows responds with its MAC address     |

### Inference
ARP establishes the mapping between IPv4 addresses and MAC addresses on the local network. Because ARP does not inherently authenticate these mappings, malicious ARP responses can potentially be used to redirect local network traffic.

### ARP capture
<img width="836" height="444" alt="image" src="https://github.com/user-attachments/assets/8bf6e2ad-0e54-4a5a-8031-05a998235b6b" />

