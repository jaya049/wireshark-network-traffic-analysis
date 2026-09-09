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
### ARP capture
Successfully captured the ARP exchange between Kali VM and Windows VM.
<img width="836" height="444" alt="image" src="https://github.com/user-attachments/assets/8bf6e2ad-0e54-4a5a-8031-05a998235b6b" />
| Packet | ARP message                                     | Meaning                                   |
| ------ | ----------------------------------------------- | ----------------------------------------- |
| 11     | `Who has 192.168.213.101? Tell 192.168.213.102` | Windows asks: "Who owns 192.168.213.101?" |
| 12     | `192.168.213.101 is at 08:00:27:01:03:3a`       | Kali responds with its MAC address        |
| 14     | `Who has 192.168.213.102? Tell 192.168.213.101` | Kali asks: "Who owns 192.168.213.102?"    |
| 16     | `192.168.213.102 is at 08:00:27:94:c0:d9`       | Windows responds with its MAC address     |

### Inference
ARP establishes the mapping between IPv4 addresses and MAC addresses on the local network. Because ARP does not inherently authenticate these mappings, malicious ARP responses can potentially be used to redirect local network traffic.

## Phase 3 : DNS Analysis
### Traffic Generation
DNS traffic was generated from the Kali Linux VM using `nslookup`.
From Kali : `nslookup example.com `
ie, queries to 8.8.8.8, Google's public DNS resolver.
### DNS Capture
Query:
<img width="958" height="429" alt="image" src="https://github.com/user-attachments/assets/b30e46f4-ad3c-41e9-9a39-d6ca72a21382" />

Response: 
<img width="930" height="452" alt="image" src="https://github.com/user-attachments/assets/2e926007-6c63-43af-95a7-27ba241f5447" />


### Inference
The capture shows DNS A and AAAA queries from Kali (10.0.2.15) to the DNS resolver (8.8.8.8) and the corresponding responses containing IPv4 and IPv6 records.
- Ipv4 result for standard A Query
- An IPv6 result for the AAAA query.

## Phase 4 : Http Analysis
### Http get analysis
<img width="847" height="438" alt="image" src="https://github.com/user-attachments/assets/0c15cd58-78ec-4665-b7f3-dfabf0a1c658" />

Windows 10                         Kali
192.168.213.102                    192.168.213.101
       │                                  │
       │──── GET / HTTP/1.1 ─────────────>│
       │<─── HTTP/1.0 200 OK ─────────────│

What packets show:
| Packet | Traffic              | Meaning                                  |
| ------ | -------------------- | ---------------------------------------- |
| 7      | `GET / HTTP/1.1`     | Windows requested the web page from Kali |
| 10     | `HTTP/1.0 200 OK`    | Kali successfully returned the page      |
| 14     | `GET /favicon.ico`   | Browser requested the site's favicon     |
| 17     | `404 File not found` | Kali doesn't have a favicon              |
| 27     | `HTTP/1.0 200 OK`    | Another successful HTTP response         |

### TCP Connection check
TCP Connection established between Windows and Kali

Windows 10                         Kali
192.168.213.102                    192.168.213.101

      │
      │──── SYN ──────────────────>│  Packet 1
      │<─── SYN, ACK ──────────────│  Packet 2
      │──── ACK ──────────────────>│  Packet 3
      │
      
Packet 1:

<img width="846" height="443" alt="image" src="https://github.com/user-attachments/assets/c3984253-a380-4fad-afe0-a73ff99a0ebe" />

Packet 2:

<img width="856" height="356" alt="image" src="https://github.com/user-attachments/assets/8347a7f5-48db-4d13-8ae4-ab64e8d520fa" />

Packet 3:

<img width="883" height="382" alt="image" src="https://github.com/user-attachments/assets/c7e3f9db-7ce7-43fc-999d-c1fe9b994191" />


