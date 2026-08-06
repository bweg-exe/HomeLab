# Enterprise Multi-Site Network & Security Lab

## Overview
A virtualized, multi-zone enterprise network architecture built using **pfSense 2.8**, **Oracle VirtualBox**, and **Lubuntu**. This project demonstrates multi-site connectivity via an IKEv2 IPsec VPN, strict network segmentation, DNS-level threat filtering, and public service publishing via NAT.

---

## Network Architecture Topology

![Network Diagram](.images/HomeLab/labdiagram.png)

### Network Segmentation & Subnets
| Zone / Site | Interface | Subnet | Description |
| :--- | :--- | :--- | :--- |
| **WAN Cloud** | `vtnet0` | `192.168.1.0/24` | Simulated Public Internet / Gateway |
| **HomeOffice - LAN** | `vtnet1` | `192.168.10.0/24` | Internal Sales Network |
| **HomeOffice - OPT1** | `vtnet2` | `192.168.20.0/24` | Restricted Finance Network |
| **HomeOffice - OPT2** | `vtnet3` | `192.168.50.0/24` | Isolated DMZ (Nginx Web Server) |
| **RemoteSite - LAN** | `vtnet1` | `192.168.60.0/24` | Branch Office Client Network |

---

## Key Features & Security Policies

* **IKEv2 Site-to-Site IPsec VPN:** Connects `HomeOffice` (`192.168.50.0/24`) and `RemoteSite` (`192.168.60.0/24`) over Phase 1/Phase 2 tunnels with AES-GCM encryption.
* **DNSBL Threat Filtering (pfBlockerNG):** Centralized ad/malware domain sinkholing at the DNS layer.
* **Web Publishing (NAT Port Forwarding):** Port-forwarding WAN port 443 to an isolated Nginx Web Server inside the Corporate DMZ, secured with self-signed TLS certificates.
* **Granular Firewall Policy:** Enforces default-deny rulebases between internal VLANs/subnets to prevent unauthorized lateral movement.

---

## Verification & Traffic Analysis (Wireshark & Logs)

### 1. Site-to-Site IPsec VPN Validation
The IKEv2 tunnel was verified active between WAN endpoints `192.168.1.60` and `192.168.1.189`. 

![IPsec Status](.images/HomeLab/ipsectunnelstatus.png)

*Wireshark capture showing ESP-encrypted traffic crossing the simulated WAN during cross-tunnel pings:*
![Wireshark ESP Capture](.images/HomeLab/ipsecwiresharkpcap.png)

### 2. DNSBL Sinkhole Proof
DNS queries from internal clients for restricted domains return the local sinkhole loopback address, preventing outbound connections.

![DNSBL Capture](.images/HomeLab/dnsblwiresharkpcap.png)

*NAT Port Forwarding & Web DMZ Verification: In Progress / Pending Final Wireshark Capture*

---

## Tools & Technologies Used
* **Firewalls / Routers:** pfSense 2.8.1 (FreeBSD)
* **Virtualization:** Oracle VirtualBox (Internal Networks)
* **Packet Analysis:** Wireshark
* **Services:** Nginx, OpenSSL, pfBlockerNG, Unbound DNS
