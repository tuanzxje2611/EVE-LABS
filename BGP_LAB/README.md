# Advanced BGP Lab: Confederation, Traffic Engineering & GRE Tunneling

<img width="926" height="588" alt="Screenshot 2025-11-26 215205" src="https://github.com/user-attachments/assets/8a1df3aa-9e7c-490b-a582-63dd09a2885f" />

## 📖 Overview
This project is a comprehensive network simulation designed to demonstrate advanced **BGP (Border Gateway Protocol)** concepts. It simulates a Service Provider and Enterprise environment utilizing multiple Autonomous Systems (AS), IPv4/IPv6 Dual Stack, and complex routing policies to manipulate traffic flow.

The topology highlights the use of **BGP Confederations** to scale iBGP mesh and **GRE Tunnels** to extend BGP peering over non-BGP transit networks.

## 🏗️ Network Architecture

The network is divided into several Autonomous Systems with distinct roles:

* **AS 999 (ISP):** Acts as the upstream Internet Service Provider providing IPv4 and IPv6 connectivity.
* **AS 1 (Edge):** The main enterprise edge router (R1) connecting to the ISP and downstream transit systems.
* **AS 23 (Transit):** An internal transit AS running iBGP between R2 and R3.
* **AS 4567 (Confederation):** A large AS split into sub-autonomous systems for scalability:
    * **Sub-AS 45:** Contains R4 and R5.
    * **Sub-AS 67:** Contains R6 and R7.
* **AS 65505.9 (Remote Site):** A remote site (R9) connected via a tunnel.
* **Transit Router (R8):** Provides underlay IP connectivity for the GRE tunnel but does not participate in BGP.

## 🚀 Key Technologies & Features

### 1. BGP Path Manipulation (Traffic Engineering)
Demonstration of various BGP attributes to influence routing decisions:
* **Weight:** Applied on **R4** to prefer specific outbound paths locally.
* **Local Preference:** Configured on **R2** (set to 750) to influence outbound traffic for the entire AS 23.
* **MED (Multi-Exit Discriminator):** Configured on **R3** (set to 800) to suggest preferred entry points to neighboring ASs.
* **AS Path Prepending:** Applied on **R4** to artificially lengthen the AS path, making it less preferred for inbound traffic from R1.

### 2. BGP Confederations
* Implementation of **Sub-AS 45** and **Sub-AS 67** within the main **AS 4567**.
* Configuration of `bgp confederation identifier` and `bgp confederation peer` on R4, R5, R6, and R7 to maintain full mesh connectivity logic without the scalability penalty.

### 3. Overlay Networking (GRE & eBGP Multihop)
* Establishment of a **GRE Tunnel (Tunnel0)** between **R5** and **R9**.
* Running an **eBGP session** over the GRE tunnel allows AS 4567 and AS 65505.9 to exchange routes transparently over R8 (which is only running OSPF).

### 4. IPv6 Implementation
* Dual-stack configuration on the **ISP** and **R1** routers.
* IPv6 BGP peering activation and prefix advertisement.

## 🛠️ Device Configuration Summary

| Device | Role | AS Number | Protocols | Key Features |
| :--- | :--- | :--- | :--- | :--- |
| **ISP** | Internet Provider | 999 | BGP, IPv6 | Redistribute Connected, IPv6 Peering |
| **R1** | Enterprise Edge | 1 | BGP, IPv6 | Dual-stack Peering, Hub for AS connections |
| **R2** | Transit Router | 23 | OSPF, iBGP | Route-map (Local Pref) |
| **R3** | Transit Router | 23 | OSPF, iBGP | Route-map (MED) |
| **R4** | Confed Edge | 4567 (Sub 45) | OSPF, BGP | Route-map (Weight, Prepend), Confed Peer |
| **R5** | Confed/Tunnel Hub | 4567 (Sub 45) | OSPF, BGP | GRE Tunnel endpoint, eBGP Multihop to R9 |
| **R6** | Confed Core | 4567 (Sub 67) | OSPF, BGP | Confederation Internal Peer |
| **R7** | Confed Core | 4567 (Sub 67) | OSPF, BGP | Confederation Internal Peer |
| **R8** | Underlay Transport | N/A | OSPF | IP Transport for GRE (No BGP) |
| **R9** | Remote Spoke | 65505.9 | BGP | GRE Tunnel endpoint, 4-byte ASN |

## 💻 How to Run
1.  Import the topology into **EVE-NG**, **GNS3**, or **PNETLab**.
2.  Load the configuration files provided in the `/configs` folder to the respective nodes.
3.  Verify OSPF neighbor adjacencies first (`show ip ospf neighbor`).
4.  Verify BGP peerings (`show ip bgp summary`).
5.  Check the routing table for manipulated paths (e.g., check if R1 prefers the path via R2 or R3 based on the policies).

## 📝 Usage Commands
* **Verify BGP Summary:** `show ip bgp summary`
* **Check Received Routes:** `show ip bgp neighbors <IP> routes`
* **Check Advertised Routes:** `show ip bgp neighbors <IP> advertised-routes`
* **Verify Tunnel Status:** `show interface tunnel 0`


