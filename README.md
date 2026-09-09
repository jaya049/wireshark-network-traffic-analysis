# wireshark-network-traffic-analysis
Hands-on network traffic analysis and security investigation using Wireshark in an isolated VirtualBox lab. Analyzes ARP, ICMP, DNS, TCP, HTTP, and Nmap-generated traffic to identify network behavior, communication patterns, and potential reconnaissance activity.

# Setup

                 Windows Host PC
                 ┌──────────────┐
                 │  Wireshark   │
                 │   Capture    │
                 └──────┬───────┘
                        │
              VirtualBox Host-Only
                  Network
                 192.168.x.x
                        │
          ┌─────────────┴─────────────┐
          │                           │
     Kali Linux                  Windows 10
       VM                          VM
    Nmap / tools                 Target

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

