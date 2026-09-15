# CCNA (200-301 v1.1) Full Reference Guide, Cheatsheet & Lab Companion

---

## Chapter 1: Network Fundamentals

### 1.1 Network Architecture, Topologies & Models
*   **Three-Tier Campus Hierarchical Model:**
    *   **Core Layer:** High-speed backbone switching/routing fabric optimized exclusively for packet switching speed; avoids CPU-intensive packet manipulation, filtering, or ACL processing.
    *   **Distribution Layer:** Policy-based boundaries, routing, packet filtering (ACLs), QoS policies, VLAN termination, and broadcast domain boundaries.
    *   **Access Layer:** Direct network connectivity endpoints for workgroup clients, IP phones, and Wireless Access Points; handles port security, STP edge ports, and PoE delivery.
    *   *Example:* Access switches deliver 802.1Q trunks up to redundant Distribution layer switches, which terminate VLAN SVIs and route packets up to dual Core layer backbones.
*   **Two-Tier (Collapsed Core) Model:** Merges the Core and Distribution layers into a consolidated layer on a pair of multilayer switches. Ideal for small-to-medium networks to reduce cost, rack footprint, and operational complexity while maintaining high availability.
    *   *Example:* Connecting access switch uplinks straight to a redundant pair of L3 switches handling both L3 Inter-VLAN routing and uplinks to the edge firewall.
*   **Spine-Leaf Architecture:** Modern leaf-spine fabric designed for East-West data center traffic flows:
    *   *Spine Switches:* Form an all-to-all non-blocking backbone. Spine switches connect exclusively to leaf switches, never to other spines.
    *   *Leaf Switches:* Connect to every spine switch and aggregate end systems (bare-metal servers, hypervisors, storage clusters).
    *   *Latency Traversal:* Every server-to-server path is deterministic and spans precisely two hops (Leaf → Spine → Leaf).
*   **WAN Topologies:**
    *   *Point-to-Point (P2P):* Dedicated serial, leased line, or Layer 2 tunnel between two endpoints.
    *   *Hub-and-Spoke (Star):* Multiple remote spoke sites communicate with a central hub node; spoke-to-spoke transit requires hair-pinning through the hub.
    *   *Full Mesh:* Every node maintains a direct physical or logical connection to every other node; link formula: `n(n-1)/2`. High redundancy, high cost.
    *   *Partial Mesh:* Selective redundant links configured between high-priority nodes to balance availability and transit costs.
*   **SOHO (Small Office / Home Office) Network Architecture:** Integrated edge hardware uniting routing, stateful firewalling, NAT overload, manageable switching, and wireless connectivity into a unified deployment.
    *   *Example:* A gateway router terminates PPPoE, handles stateful filtering, and routes traffic over an 802.1Q trunk down to a managed switch and wireless access point.
*   **Cloud Computing Models & Service Architectures:**
    *   *On-Premises:* Full physical, hypervisor, and network stack owned, powered, cooled, and maintained locally.
    *   *IaaS (Infrastructure as a Service):* Cloud vendor provisions compute, virtualization, storage, and networking; user manages OS, runtimes, and apps (e.g., AWS EC2).
    *   *PaaS (Platform as a Service):* Vendor manages OS, networking, and scaling; user supplies and deploys application code (e.g., AWS Elastic Beanstalk).
    *   *SaaS (Software as a Service):* Fully vendor-managed application delivery (e.g., Microsoft 365, Google Workspace).
    *   *Public Cloud:* Multi-tenant shared infrastructure managed by hyperscalers (AWS, Azure, GCP).
    *   *Private Cloud:* Dedicated single-tenant infrastructure configured on-premises or via private data centers.
    *   *Hybrid Cloud:* Seamless integration bridging on-premises infrastructure with public cloud instances via IPsec or direct interconnects.

### 1.2 Layer 1 (Physical Media, Cabling & Interfaces)
*   **Copper Ethernet Cabling (Twisted Pair):**
    *   *Cat5e:* Up to 1 Gbps at 100 MHz, max run distance 100 meters.
    *   *Cat6:* 1 Gbps up to 100m, 10 Gbps supported up to 55m (250 MHz).
    *   *Cat6a:* Full 10 Gbps support up to the maximum 100m distance (500 MHz); enhanced shielding against alien crosstalk.
*   **Pinouts & Cable Types:**
    *   *T568A vs. T568B:* Standard color code sequences. T568B order: White/Orange, Orange, White/Green, Blue, White/Blue, Green, White/Brown, Brown.
    *   *Straight-Through Cable:* Both ends terminated using the same standard (T568B to T568B); connects differing device types (Switch to Host, Switch to Router).
    *   *Crossover Cable:* One end T568A, opposite end T568B; connects like devices without auto-negotiation (Switch to Switch, Router to PC).
    *   *Auto-MDIX (Automatic Medium-Dependent Interface Crossover):* NIC firmware algorithm that automatically detects cable transmission pairs and corrects internal pinouts dynamically.
    *   *Rollover (Console) Cable:* RJ45-to-DB9/USB pinout reversing pin order completely; dedicated to Out-of-Band (OOB) serial terminal access.
*   **Fiber Optic Cabling:**
    *   *Single-Mode Fiber (SMF):* Extremely thin silica core (~9 µm); utilizes laser diode transmitters; eliminates modal dispersion; used for long-haul and ISP backbone spans (tens of kilometers).
    *   *Multi-Mode Fiber (MMF):* Wider core (50–62.5 µm); utilizes LED or VCSEL light sources; light rays bounce at variable angles causing modal dispersion; restricted to local campus and datacenter runs (typically up to 300–500m).
*   **Pluggable Transceivers:**
    *   *SFP (Small Form-factor Pluggable):* 1 Gbps hot-swappable optical or copper modular interface.
    *   *SFP+:* 10 Gbps modular transceiver retaining the standard SFP physical footprint.
    *   *QSFP / QSFP28:* Quad SFP packaging delivering 40 Gbps and 100 Gbps channel bonding.
*   **PoE (Power over Ethernet - IEEE Standards):**
    *   *802.3af (Type 1 - PoE):* Up to 15.4W at the PSE (Power Sourcing Equipment), minimum 12.95W delivered at the PD (Powered Device).
    *   *802.3at (Type 2 - PoE+):* Up to 30W at PSE, 25.5W delivered at the PD. Powers dual-band Wi-Fi 6 APs and PTZ cameras.
    *   *802.3bt (Type 3/4 - PoE++ / 4PPoE):* Delivers 60W to 90W+ across all four twisted pairs for smart displays, servers, and high-wattage IoT hardware.
*   **Physical Interface Diagnostics & Errors:**
    *   *Speed/Duplex Mismatch:* One side locked at 100/Full, partner auto-negotiating to 100/Half; generates collisions, late collisions, and frame check sequence (FCS) errors.
    *   *Runts:* Packets smaller than the minimum 64-byte Ethernet standard, typically caused by collisions or cable noise.
    *   *Giants:* Packets exceeding the maximum standard MTU (e.g., larger than 1518 bytes untagged) arriving on non-jumbo frame interfaces.
    *   *FCS / CRC Errors:* Frames failing checksum validation, identifying faulty copper termination, EMI interference, or bad transceivers.

### 1.3 Layer 2 Foundations & Encapsulation
*   **OSI Reference Model vs. TCP/IP Stack:**
    *   *OSI Layers:* 7-Application, 6-Presentation, 5-Session, 4-Transport, 3-Network, 2-Data Link, 1-Physical.
    *   *TCP/IP Layers:* 4-Application, 3-Transport, 2-Internet, 1-Network Access.
*   **PDU (Protocol Data Unit) Terminology:**
    *   L4: Segment (TCP) / Datagram (UDP).
    *   L3: Packet (IP).
    *   L2: Frame (Ethernet).
    *   L1: Bits (Raw electrical/optical transmission).
*   **Encapsulation / Decapsulation:** Process of wrapping higher-level PDUs with lower-level headers and trailers on egress, and stripping them layer-by-layer on ingress.
*   **MAC Address (EUI-48 Format):** 48-bit hex address formatted as `XX:XX:XX:XX:XX:XX` or `XXXX.XXXX.XXXX`.
    *   *OUI (Organizationally Unique Identifier):* First 24 bits designating the hardware manufacturer.
    *   *Device Serial:* Final 24 bits uniquely assigned by the manufacturer.
*   **Collision Domains vs. Broadcast Domains:**
    *   *Collision Domain:* Physical segment where two concurrent frame transmissions result in bit collisions. Every switch port running Full Duplex is its own independent collision domain.
    *   *Broadcast Domain:* Logical segment where a single broadcast frame propagates to every host. Delimited by Layer 3 devices (routers) and Layer 2 VLAN boundaries.
*   **Transmission Methods:**
    *   *Unicast:* Point-to-point transmission addressed to one specific host.
    *   *Multicast:* Point-to-multipoint transmission addressed to a subscribed group (e.g., OSPF `224.0.0.5`).
    *   *Broadcast:* Point-to-all transmission within a broadcast domain (L2 MAC: `FF:FF:FF:FF:FF:FF`, L3 IPv4: `255.255.255.255`).

### 1.4 IPv4 Addressing, Subnetting & Mathematics
*   **IPv4 Header Fields:**
    *   *Version (4 bits):* IP version (`0100` for IPv4).
    *   *IHL (4 bits):* Internet Header Length (minimum 20 bytes).
    *   *Type of Service / DSCP (8 bits):* Quality of Service marking and congestion notification.
    *   *Total Length (16 bits):* Full size of the IP packet including header and data payload.
    *   *Identification, Flags, Fragment Offset (32 bits total):* Controls packet fragmentation and reconstruction.
    *   *TTL (Time-to-Live, 8 bits):* Decremented by 1 at every L3 hop; prevents infinite loops by dropping packets at TTL=0 with ICMP Type 11 (Time Exceeded).
    *   *Protocol (8 bits):* Encapsulated L4 protocol (1 = ICMP, 6 = TCP, 17 = UDP, 89 = OSPF).
    *   *Header Checksum (16 bits):* Error verification for the IPv4 header only.
    *   *Source & Destination IP Addresses (32 bits each).*
*   **Subnetting Reference Table:**

| CIDR | Subnet Mask | Total IPs | Usable Hosts | Block Size (Magic Number) |
|---|---|---|---|---|
| `/24` | `255.255.255.0` | 256 | 254 | 256 |
| `/25` | `255.255.255.128` | 128 | 126 | 128 |
| `/26` | `255.255.255.192` | 64 | 62 | 64 |
| `/27` | `255.255.255.224` | 32 | 30 | 32 |
| `/28` | `255.255.255.240` | 16 | 14 | 16 |
| `/29` | `255.255.255.248` | 8 | 6 | 8 |
| `/30` | `255.255.255.252` | 4 | 2 | 4 |
| `/31` | `255.255.255.254` | 2 | 2 (RFC 3021 Point-to-Point) | 2 |
| `/32` | `255.255.255.255` | 1 | 1 (Host / Loopback) | 1 |

*   **Subnetting Calculation Rules:**
    *   *Total IPs:* `2^(32 - prefix)`
    *   *Usable Hosts:* `2^(32 - prefix) - 2` (subtract Network ID and Broadcast address, except on `/31`).
    *   *Magic Number (Block Size):* `256 - [interesting octet value of subnet mask]`.
*   **Private Address Space (RFC 1918):**
    *   Class A: `10.0.0.0/8` (`10.0.0.0` to `10.255.255.255`)
    *   Class B: `172.16.0.0/12` (`172.16.0.0` to `172.31.255.255`)
    *   Class C: `192.168.0.0/16` (`192.168.0.0` to `192.168.255.255`)
*   **Special IPv4 Ranges:**
    *   *Loopback:* `127.0.0.0/8` (Internal stack diagnostics).
    *   *APIPA (Link-Local):* `169.254.0.0/16` (Host self-assigned when DHCP discovery times out).
    *   *Carrier-Grade NAT (RFC 6598):* `100.64.0.0/10` (ISP shared address pool).

### 1.5 IPv6 Fundamentals & Architecture
*   **IPv6 Structure:** 128-bit address written as eight groups of four hexadecimal digits separated by colons (e.g., `2001:0db8:85a3:0000:0000:8a2e:0370:7334`).
*   **Compression Rules:**
    1.  *Omit Leading Zeros:* `0000` → `0`, `0db8` → `db8`.
    2.  *Double Colon (::):* Replace a single contiguous sequence of all-zero blocks with `::` (can be applied only once per address).
    *   *Compressed Result:* `2001:db8:85a3::8a2e:370:7334`.
*   **IPv6 Address Types:**
    *   *Global Unicast Address (GUA):* Publicly routable, internet-facing scope; begins with binary `001` (starts with `2000::/3`).
    *   *Unique Local Address (ULA):* Private internal routable scope; starts with `fc00::/7` (practically `fd00::/8`).
    *   *Link-Local Address (LLA):* Mandatory per interface; non-routable beyond local link; used for routing protocols and neighbor discovery; starts with `fe80::/10`.
    *   *Multicast:* Replaces broadcast; starts with `ff00::/8` (e.g., `ff02::1` = All Nodes, `ff02::2` = All Routers, `ff02::5` = OSPFv3 All Routers).
    *   *Loopback:* `::1/128`.
    *   *Unspecified Address:* `::/128` (Used during DAD).
*   **EUI-64 (Extended Unique Identifier):** Automatically derives a 64-bit interface identifier from a 48-bit MAC address:
    1.  Split 48-bit MAC into two equal 24-bit halves.
    2.  Insert hexadecimal `FF:FE` in the middle.
    3.  Invert the 7th bit (Universal/Local bit) of the first byte.
*   **NDP (Neighbor Discovery Protocol - RFC 4861):**
    *   *Router Solicitation (RS):* Client queries local routers for addressing info (multicast to `ff02::2`).
    *   *Router Advertisement (RA):* Routers periodically announce prefixes, default gateway, and MTU (multicast to `ff02::1`).
    *   *Neighbor Solicitation (NS):* Resolves target Layer 2 MAC addresses (replaces ARP requests) and performs DAD.
    *   *Neighbor Advertisement (NA):* Layer 2 address resolution reply (replaces ARP replies).
    *   *DAD (Duplicate Address Detection):* Host sends NS for its own planned IP to ensure no other device claims it before binding.
*   **SLAAC (Stateless Address Autoconfiguration):** Client learns network prefix from ICMPv6 RA messages, then automatically constructs its 64-bit Host ID using EUI-64 or random privacy extensions without a DHCPv6 server.

### 1.6 Verification CLI Commands
```text
show ip interface brief         ! View L1 status, L2 protocol state, and assigned IPv4 addresses
show ipv6 interface brief       ! View configured IPv6 link-local and global unicast addresses per port
show interfaces <id>            ! Inspect speed, duplex, MTU, bandwidth, CRC errors, runts, and giants
show controllers <id>           ! Inspect underlying hardware controller and physical cabling layer
```

---

## Chapter 2: Network Access (Switching, VLANs & Spanning Tree)

### 2.1 Layer 2 Switch Operations
*   **MAC Address Table (CAM Table) Learning & Aging:**
    *   A switch inspects the **Source MAC** address of every incoming frame and records it in the Content Addressable Memory (CAM) table alongside the receiving physical port and VLAN ID.
    *   Entries expire dynamically when no new frames from that source MAC are seen within the aging timer (default: 300 seconds).
*   **Frame Forwarding Decisions:**
    *   *Known Unicast:* If Destination MAC exists in the CAM table for that VLAN, forward the frame out that specific port only.
    *   *Unknown Unicast Flooding:* If Destination MAC is missing, flood the frame out all ports belonging to that VLAN except the ingress port.
    *   *Broadcast Flooding:* Frames with Destination MAC `FF:FF:FF:FF:FF:FF` flood out all active ports in the VLAN except the ingress port.
    *   *Multicast Flooding:* Flooded identically to broadcasts unless IGMP Snooping is enabled to prune ports lacking active subscriptions.
*   **Switching Forwarding Architectures:**
    *   *Store-and-Forward:* Reads the entire frame, calculates the Frame Check Sequence (FCS) checksum to verify data integrity, and discards errors before forwarding. Required for ports with differing speeds.
    *   *Cut-Through:* Reads only the first 12 bytes (up through Destination MAC) and forwards the frame immediately. Minimal latency, but forwards corrupted frames.
    *   *Fragment-Free:* Reads the initial 64 bytes to ensure the frame is not a collision fragment (runt) before forwarding.

### 2.2 VLANs, Trunks & Inter-VLAN Routing
*   **VLAN (Virtual LAN - IEEE 802.1Q):** Divides a physical switch fabric into isolated, logical Layer 2 broadcast domains.
*   **Switchport Access Mode:** Forwards traffic for a single assigned VLAN; frames traverse the link strictly **untagged**.
*   **Switchport Trunk Mode:** Multiplexes traffic from multiple VLANs simultaneously across a single physical link by injecting an 802.1Q tag into the frame header.
*   **802.1Q Tag Structure (4 Bytes total):**
    *   *TPID (Tag Protocol Identifier, 16 bits):* Set to `0x8100` to indicate an 802.1Q tagged frame.
    *   *TCI (Tag Control Information, 16 bits):*
        *   *PCP (Priority Code Point, 3 bits):* Layer 2 QoS marking (CoS values 0-7).
        *   *DEI (Drop Eligible Indicator, 1 bit):* Marks frames droppable during congestion.
        *   *VLAN ID (VID, 12 bits):* Identifies the VLAN (1 to 4094 supported).
*   **Native VLAN:** The designated VLAN on an 802.1Q trunk whose frames are transmitted and received **without an 802.1Q header tag** (Default: VLAN 1).
    *   *Security Requirement:* Both ends of a trunk link must share the exact same Native VLAN configuration to prevent native VLAN mismatch errors and cross-talk vulnerabilities.
*   **Voice VLAN:** Dedicated switchport feature providing priority 802.1p CoS tagging for VoIP phones via CDP/LLDP discovery, while passing untagged PC data through the phone's internal switch port onto a standard data VLAN.
*   **DTP (Dynamic Trunking Protocol - Cisco Proprietary):** Automatically manages trunk links between Cisco switches:
    *   *Dynamic Desirable:* Actively initiates trunk negotiation.
    *   *Dynamic Auto:* Passively listens; converts to trunk only if the remote neighbor initiates (Desirable or Trunk).
    *   *Trunk (`switchport mode trunk`):* Forces static trunking; continues transmitting DTP frames unless disabled.
    *   *Nonegotiate (`switchport nonegotiate`):* Completely disables DTP packet generation on a static trunk or access port.
*   **Inter-VLAN Routing Implementations:**
    *   *Router-on-a-Stick (ROAS):* Single physical router link sliced into logical subinterfaces (`Gi0/0.10`, `Gi0/0.20`), using `encapsulation dot1Q <vid>` to route between VLANs.
    *   *Multilayer Switch (L3 Switch):* Wire-speed routing between VLANs using internal **SVIs (Switched Virtual Interfaces)** (`interface vlan 10`) backed by hardware ASICs.
    *   *Routed Port:* Disables Layer 2 switching on a multilayer switch port (`no switchport`), allowing direct IP address assignment like a hardware router.

### 2.3 Spanning Tree Protocol (STP & RSTP)
*   **Layer 2 Loops & Consequences:** Redundant Layer 2 links without loop-prevention protocols generate broadcast storms, multiple frame transmission copies, and rapid CAM table thrashing, exhausting switch CPU and memory.
*   **STP (IEEE 802.1D):** Legacy Spanning Tree protocol:
    *   *Convergence Time:* 30 to 50 seconds.
    *   *States:* Blocking → Listening (15s forward delay) → Learning (15s forward delay) → Forwarding.
*   **RSTP (IEEE 802.1w):** Rapid Spanning Tree Protocol with sub-second convergence:
    *   *States:* Discarding, Learning, Forwarding.
    *   *Port Roles:* Root Port (RP), Designated Port (DP), Alternate Port (AP - backup to RP), Backup Port (backup to DP).
*   **Root Bridge Election Mechanics:**
    *   Switch with the lowest **Bridge ID (BID)** becomes Root Bridge for the STP instance.
    *   `Bridge ID = Bridge Priority (default 32768, increments of 4096) + Extended System ID (VLAN ID) + Base MAC Address`.
*   **Root Port Path Cost:** The total accumulated link cost to reach the Root Bridge:
    *   10 Gbps = 2
    *   1 Gbps = 4
    *   100 Mbps = 19
    *   10 Mbps = 100
*   **BPDU (Bridge Protocol Data Unit):** Layer 2 control frames transmitted every 2 seconds (Hello timer) containing Bridge ID, Root Path Cost, and port states to maintain loop-free topology trees.
*   **STP Toolkit & Hardening Features:**
    *   *PortFast:* Configured on edge access ports; transitions the port immediately from Discarding straight to Forwarding, bypassing listening and learning.
    *   *BPDU Guard:* Automatically transitions a PortFast-enabled port into `err-disable` state if any BPDU is received, preventing rogue switches from attaching.
    *   *Root Guard:* Prevents downstream unauthorized switches from becoming the Root Bridge by setting ports receiving superior BPDUs to a `root-inconsistent` state.
    *   *Loop Guard:* Prevents Alternate or Root ports from becoming Designated ports due to unidirectional link failures or silent BPDU loss, placing the port in a `loop-inconsistent` state.

### 2.4 EtherChannel (Link Aggregation)
*   **EtherChannel Technology:** Aggregates up to 8 active parallel physical Ethernet links into a single logical link (Port-Channel), providing load sharing and fault tolerance without triggering STP port blocking.
*   **Link Aggregation Protocols:**
    *   *LACP (IEEE 802.3ad - Open Standard):* Modes: **Active** (initiates negotiation) and **Passive** (responds to negotiation requests only).
    *   *PAgP (Cisco Proprietary):* Modes: **Desirable** (initiates negotiation) and **Auto** (responds only).
    *   *Static (Mode On):* Forces link aggregation without negotiation packets. Prone to loops if misconfigured.
*   **Configuration Prerequisites (Must Match Across All Member Ports):**
    *   Same operational speed and duplex settings.
    *   Same switchport mode (Access or Trunk).
    *   Identical Native VLAN and identical list of Allowed VLANs on trunks.
    *   Identical Access VLAN on access ports.
*   **Load Balancing Hashes:** Traffic is distributed across member links via a mathematical hash of frame and packet headers: Source MAC, Destination MAC, Source/Dest MAC, Source/Dest IP, or L4 TCP/UDP Port numbers.

### 2.5 Discovery Protocols (CDP & LLDP)
*   **CDP (Cisco Discovery Protocol):** Cisco proprietary Layer 2 protocol enabled by default to discover device names, IP addresses, native VLANs, hardware capabilities, and IOS versions on adjacent Cisco devices.
*   **LLDP (Link Layer Discovery Protocol - IEEE 802.1AB):** Open-standard neighbor discovery protocol providing cross-vendor hardware identification and port advertisements.

### 2.6 Verification CLI Commands
```text
show mac address-table dynamic  ! Display dynamically learned MAC addresses and port mappings
show vlan brief                 ! Verify configured VLAN IDs, names, and assigned access ports
show interfaces trunk           ! Check operational trunk ports, native VLANs, and allowed VLAN lists
show spanning-tree              ! View active STP topology, root bridge BID, and port roles/states
show spanning-tree summary      ! Inspect global STP modes, PortFast defaults, and BPDU Guard status
show etherchannel summary       ! Check port-channel operational states (P: bundled in channel, D: down)
show cdp neighbors detail       ! Detailed neighbor discovery (neighbor IP, platform, software version)
show lldp neighbors detail      ! Detailed discovery output for open standard LLDP neighbors
```

---

## Chapter 3: IP Connectivity (Routing & OSPF)

### 3.1 Routing Concepts & Logic
*   **Routing Table Operation:** When forwarding a packet, the router determines the egress interface by evaluating:
    1.  **Longest Prefix Match:** Route with the most specific subnet mask (longest prefix length) is always selected first, regardless of protocol or AD (e.g., `/28` beats `/24`).
    2.  **Administrative Distance (AD):** If identical prefixes are learned from multiple different routing sources, the route with the lowest AD is installed in the routing table.
    3.  **Metric:** If identical prefixes are learned from the same routing protocol, the path with the lowest calculated metric (cost) is chosen.
*   **Administrative Distance Reference Table:**

| Routing Source | Administrative Distance (AD) |
|---|---|
| Connected Interface | `0` |
| Static Route | `1` |
| External BGP (eBGP) | `20` |
| EIGRP (Internal) | `90` |
| OSPF | `110` |
| IS-IS | `115` |
| RIP | `120` |
| EIGRP (External) | `170` |
| Internal BGP (iBGP) | `200` |
| Unusable / Unknown | `255` |

*   **Default Route (Gateway of Last Resort):** A static or dynamically learned catch-all route matching all unlisted destinations: `0.0.0.0/0` (IPv4) or `::/0` (IPv6).
*   **Floating Static Route:** A backup static route configured with an Administrative Distance higher than the primary source (e.g., `ip route 0.0.0.0 0.0.0.0 1.1.1.2 200`), remaining dormant until the primary link fails.

### 3.2 OSPF (Open Shortest Path First - v2 for IPv4, v3 for IPv6)
*   **Link-State Routing Architecture:** Routers advertise the exact status of their directly connected links using Link-State Advertisements (LSAs), building a complete topology map in their Link-State Database (LSDB). Each router independently runs the Dijkstra SPF algorithm to calculate a loop-free, shortest-path tree to every destination.
*   **Router ID (RID) Selection Order:**
    1.  Manual CLI configuration: `router-id X.X.X.X`.
    2.  Highest IPv4 address on an active **Loopback** interface.
    3.  Highest IPv4 address on an active **Physical** interface in `up/up` state.
*   **Two-Tier Hierarchical Area Design:**
    *   **Area 0 (Backbone Area):** Central transit core. All non-backbone areas must connect directly to Area 0 to prevent inter-area routing loops.
    *   **Standard (Non-Backbone) Areas:** Sub-areas grouping devices to localize SPF calculations and limit LSA flooding scope.
    *   *ABR (Area Border Router):* Router with interfaces terminating in both Area 0 and a non-backbone area; originates Type 3 Summary LSAs.
    *   *ASBR (Autonomous System Boundary Router):* Router that redistributes routes learned from external sources (e.g., BGP, static routes) into OSPF via Type 5 External LSAs.
*   **OSPF Cost Metric Calculation:**
    *   `Cost = Reference Bandwidth / Interface Bandwidth`
    *   *Default Reference Bandwidth:* 100 Mbps (`10^8 bps`). FastEthernet and Gigabit interfaces evaluate to the same minimum cost of 1 unless updated globally via `auto-cost reference-bandwidth 1000` (or `10000` for 10 Gbps).
*   **OSPF Neighbor Adjacency States:**
    1.  *Down:* Initial state; no OSPF Hellos received from the neighbor.
    2.  *Init:* Hello packet received, but local Router ID is not listed in the neighbor's active neighbor list.
    3.  *2-Way:* Bidirectional communication confirmed (local RID visible in neighbor's Hello). DR/BDR elections occur on broadcast links.
    4.  *ExStart:* Routers negotiate Master/Slave roles and Initial Sequence Numbers for Database Description (DBD) synchronization.
    5.  *Exchange:* Routers exchange DBD packets describing LSDB contents.
    6.  *Loading:* Routers request newer or missing LSAs via Link-State Requests (LSR), received via Link-State Updates (LSU).
    7.  *Full:* LSDB is completely synchronized across neighbors; normal SPF routing state achieved.
*   **DR (Designated Router) & BDR (Backup Designated Router):**
    *   Elected on Multi-Access (Broadcast) networks to limit full mesh adjacency counts from `n(n-1)/2` to linear adjacencies with the DR/BDR.
    *   *Election Criteria:* Highest interface OSPF Priority (default: 1, range: 0-255; 0 = never participates) → Highest Router ID breaks ties. Non-preemptive.
    *   *DROther Routers:* Routers that are neither DR nor BDR form `FULL` adjacencies only with the DR and BDR; they stay in `2-WAY` state with other DROther routers.
    *   *Multicast Communications:*
        *   `224.0.0.5` (All OSPF Routers): Monitored by every active OSPF interface.
        *   `224.0.0.6` (All DR Routers): Monitored exclusively by the DR and BDR.
*   **OSPF Network Types:**
    *   *Broadcast:* Ethernet links; elects DR/BDR; Hello timer = 10s, Dead = 40s.
    *   *Point-to-Point:* Serial or routed P2P links; skips DR/BDR election; forms direct `FULL` adjacencies; Hello = 10s, Dead = 40s.
*   **OSPF Passive Interface:** Blocks transmission of outbound OSPF Hello packets on an interface while continuing to announce that interface's subnet in LSAs (`passive-interface <id>`).

### 3.3 OSPF Adjacency Troubleshooting Checklist
If an OSPF neighbor relationship fails to reach the `FULL` state, verify these mandatory match parameters:
*   [ ] **Subnet & Mask:** Both interfaces must belong to the identical IP subnet and prefix length.
*   [ ] **Hello & Dead Timers:** Hello intervals and Dead intervals must match identically.
*   [ ] **Area ID & Type:** Interface Area assignment must match (and both sides must agree on area flags, e.g., Stub/NSSA).
*   [ ] **Authentication:** Authentication type and shared pre-shared key must match.
*   [ ] **Unique Router IDs:** Neighbors cannot use the same 32-bit Router ID.
*   [ ] **MTU Matching:** If interface MTUs do not match, the adjacency will hang in **ExStart / Exchange** state indefinitely.
*   [ ] **Network Type Compatibility:** Broadcast vs. Point-to-Point settings must match.

### 3.4 Verification CLI Commands
```text
show ip route                   ! View routing table entries, sources (C, S, O), and metric costs
show ip route ospf              ! Filter the routing table strictly for OSPF-derived routes
show ip ospf neighbor           ! Check neighbor Router IDs, states (FULL/2-WAY), and DR/BDR roles
show ip ospf interface <id>     ! View interface Area ID, cost, network type, priority, and timers
show ip protocols               ! Verify active routing protocols, router IDs, and networks being advertised
```

---

## Chapter 4: IP Services & Network Architecture

### 4.1 Well-Known Ports Reference Table

| Port Number | Protocol | Application Service | Transport Layer |
|---|---|---|---|
| **20 / 21** | FTP | File Transfer Protocol (Data / Control) | TCP |
| **22** | SSH / SFTP | Secure Shell / Secure FTP | TCP |
| **23** | Telnet | Unencrypted Remote Terminal | TCP |
| **25** | SMTP | Simple Mail Transfer Protocol | TCP |
| **53** | DNS | Domain Name System | UDP (Queries) / TCP (Zone Transfers) |
| **67 / 68** | DHCP | Dynamic Host Configuration (67: Server, 68: Client) | UDP |
| **69** | TFTP | Trivial File Transfer Protocol | UDP |
| **80** | HTTP | Hypertext Transfer Protocol | TCP |
| **110** | POP3 | Post Office Protocol v3 | TCP |
| **123** | NTP | Network Time Protocol | UDP |
| **143** | IMAP | Internet Message Access Protocol | TCP |
| **161 / 162** | SNMP | Simple Network Management (161: Polling, 162: Traps) | UDP |
| **443** | HTTPS | Hypertext Transfer Protocol Secure (TLS) | TCP |
| **514** | Syslog | System Logging Service | UDP |

### 4.2 Dynamic Host Configuration Protocol (DHCP)
*   **The DORA Process:**
    *   **Discover:** Client broadcasts Layer 2 (`FF:FF:FF:FF:FF:FF`) and Layer 3 (`255.255.255.255`) packets seeking any available DHCP server on UDP port 67.
    *   **Offer:** DHCP server reserves an unassigned IP and unicasts/broadcasts addressing parameters to the client on UDP port 68.
    *   **Request:** Client broadcasts an acceptance confirming the lease of the offered IP configuration.
    *   **Acknowledge (ACK):** Server confirms the lease assignment and records the client's MAC address in its lease database.
*   **DHCP Relay Agent (IP Helper Address):** Intercepts client UDP broadcast requests on local subnets and converts them into routable unicast packets destined for a remote centralized DHCP server (`ip helper-address <server-ip>`).

### 4.3 Domain Name System (DNS)
*   **Resolution Process:** Hierarchical naming structure converting Fully Qualified Domain Names (FQDNs) into routable IP addresses.
*   **Core Resource Record Types:**
    *   *A Record:* Maps a hostname to an IPv4 address.
    *   *AAAA Record:* Maps a hostname to an IPv6 address.
    *   *CNAME (Canonical Name):* Creates an alias pointing one domain name to another domain name.
    *   *PTR (Pointer):* Resolves an IP address back to a hostname (Reverse DNS lookup).
    *   *MX (Mail Exchange):* Designates incoming mail servers for a domain.

### 4.4 Network Address Translation (NAT)
*   **NAT Terminology (RFC 2663):**
    *   *Inside Local:* The internal private IP address assigned to an endpoint on the LAN.
    *   *Inside Global:* The public IP address representing the internal host to the external Internet.
    *   *Outside Local:* The IP address of an outside host as observed by inside network clients.
    *   *Outside Global:* The actual public IP address assigned to an external host on the Internet.
*   **NAT Variants:**
    *   *Static NAT:* Permanent 1-to-1 mapping linking an inside private IP to a dedicated outside public IP (commonly used for DMZ servers).
    *   *Dynamic NAT:* Maps inside private IPs to an allocated pool of public IPs on a first-come, first-served basis.
    *   *PAT (Port Address Translation / NAT Overload):* Maps thousands of inside private IPs to a single shared public IP by multiplexing unique Layer 4 source port numbers.

### 4.5 First Hop Redundancy Protocols (FHRP)
*   **HSRP (Hot Standby Router Protocol - Cisco Proprietary):** Multiple physical routers share a Virtual IP and Virtual MAC (`0000.0c07.acXX`, where XX is the hex group number).
    *   *Active Router:* Forwards traffic sent to the virtual default gateway IP.
    *   *Standby Router:* Monitors active router keepalives and assumes forwarding if the active fails.
    *   *Preemption:* Allows a router with a higher configured priority to immediately reclaim the Active role when it comes back online (`standby X preempt`).
*   **VRRP (Virtual Router Redundancy Protocol - IEEE Standard / RFC 5798):** Open-standard default gateway redundancy protocol.
    *   *Master Router:* Actively forwards traffic.
    *   *Backup Routers:* Monitor the master router. Virtual MAC: `0000.5e00.01XX`.

### 4.6 Network Management Protocols (NTP, SNMP, Syslog)
*   **NTP (Network Time Protocol):** Synchronizes clocks across networking equipment via UDP port 123.
    *   *Stratum Levels:* Accuracy distance from reference clocks: Stratum 0 (Atomic/GPS clocks), Stratum 1 (Servers directly attached to Stratum 0), Stratum 2 (Servers synchronized to Stratum 1).
*   **SNMP (Simple Network Management Protocol):** Network monitoring system communicating over UDP 161 (polling) and UDP 162 (traps).
    *   *Components:* NMS (Manager), SNMP Agent (device software), MIB (database of device metrics), OID (Object Identifier string).
    *   *SNMPv1 / SNMPv2c:* Insecure; uses cleartext community strings (Read-Only / Read-Write).
    *   *SNMPv3:* Enterprise secure version offering Message Integrity, Authentication (HMAC-SHA/MD5), and Encryption (AES/DES).
*   **Syslog Architecture:** Central logging format utilizing UDP port 514.
    *   *Severity Levels (0 through 7):*
        *   `0`: Emergency (System completely unusable)
        *   `1`: Alert (Immediate intervention required)
        *   `2`: Critical (Critical hardware/software conditions)
        *   `3`: Error (Operational errors)
        *   `4`: Warning (Warning conditions)
        *   `5`: Notice (Normal but significant operational events)
        *   `6`: Informational (Standard informational status messages)
        *   `7`: Debugging (Detailed diagnostics, heavy CPU usage)

### 4.7 Quality of Service (QoS)
*   **Network Impairments:**
    *   *Bandwidth:* Maximum throughput capacity of an interface.
    *   *Delay (Latency):* Total travel time for a packet from source to destination.
    *   *Jitter:* Variation in packet arrival delay; degrades real-time voice and video streams.
    *   *Packet Loss:* Dropped packets caused by output queue buffer exhaustion during congestion.
*   **QoS Marking & Classification Fields:**
    *   *CoS (Class of Service):* 3-bit field inside the 802.1Q tag (Layer 2, values 0-7).
    *   *DSCP (Differentiated Services Code Point):* 6-bit field in the IP header (Layer 3, values 0-63).
        *   *Default Forwarding (DF):* Best-effort service (value 0).
        *   *Assured Forwarding (AF):* Classes providing guaranteed delivery profiles with drop precedence.
        *   *Expedited Forwarding (EF):* Low-delay, low-loss, low-jitter priority queue reserved for voice payload (DSCP value 46).
*   **Congestion Management Tools:**
    *   *Queuing (CBWFQ, LLQ):* Class-Based Weighted Fair Queuing allocates minimum bandwidth guarantees. Low Latency Queuing (LLQ) adds a strict priority queue for real-time voice.
    *   *Policing vs. Shaping:*
        *   *Policing:* Immediately drops or remarks traffic exceeding configured limits; generates a jagged bandwidth utilization curve.
        *   *Shaping:* Buffers traffic bursts in software queues to smooth egress flow to a defined rate; prevents packet drops at the expense of slight delay.

### 4.8 Verification CLI Commands
```text
show ip nat translations        ! View active inside/outside NAT translations and port allocations
show ip dhcp binding            ! List all active IP leases assigned by the local Cisco DHCP server
show ntp status                 ! Verify clock synchronization, reference peer, and active stratum level
show standby brief              ! Verify HSRP state (Active/Standby), virtual IP, and preemption settings
show logging                    ! View the internal Syslog message buffer, logging levels, and log hosts
```

---

## Chapter 5: Security Fundamentals

### 5.1 Security Principles & Threat Landscapes
*   **The CIA Triad:**
    *   *Confidentiality:* Restricting sensitive data access exclusively to authenticated, authorized users (Encryption).
    *   *Integrity:* Verifying data has not been altered or forged in transit (Cryptographic Hashes).
    *   *Availability:* Ensuring network infrastructure and services remain reachable and operational (Redundancy, DDoS defense).
*   **Common Attack Methodologies:**
    *   *Man-in-the-Middle (MitM):* Attacker positions themselves between communicating parties to inspect or alter traffic.
    *   *Denial of Service (DoS / DDoS):* Flooding target resources with traffic volume or malformed packets to exhaust memory/CPU.
    *   *Reconnaissance:* Scanning networks (e.g., Nmap) to enumerate live hosts, listening ports, and OS versions.
    *   *Spoofing:* Falsifying source IP or MAC addresses to impersonate trusted devices or bypass access filters.

### 5.2 Device Hardening & AAA Framework
*   **Password Encryption Levels on Cisco IOS:**
    *   `enable password`: Legacy plaintext storage (Insecure).
    *   `service password-encryption`: Weak Type 7 reversible Vigenère cipher applied to configurations (Insecure).
    *   `enable secret`: Secures privileged EXEC access with one-way hashing algorithms (Type 5 MD5, Type 8 PBKDF2/SHA-256, or Type 9 Scrypt).
*   **Management Protocols:**
    *   *Telnet:* Unencrypted, cleartext management over TCP port 23 (Insecure).
    *   *SSH (Secure Shell):* Encrypted management session running over TCP port 22 using public/private key pairs.
*   **AAA Architecture (Authentication, Authorization, Accounting):**
    *   *Authentication:* Identity validation ("Who are you?").
    *   *Authorization:* Access policy enforcement ("What commands and configurations are you permitted to run?").
    *   *Accounting:* Activity tracking and auditing ("What commands were executed, and when?").
    *   *TACACS+ (Cisco Proprietary):* Runs on TCP port 49; encrypts the **entire** packet payload; completely separates Authentication, Authorization, and Accounting functions.
    *   *RADIUS (Open Standard / RFC 2865):* Runs on UDP ports 1812/1813 or 1645/1646; encrypts **only** the password field; combines Authentication and Authorization into a unified exchange.

### 5.3 Layer 2 Protection Mechanisms
*   **Port Security:** Restricts frame entry on switch ports based on source MAC addresses:
    *   *Static MAC:* Administrator manually binds a MAC address to an interface.
    *   *Dynamic MAC:* Switch learns the source MAC dynamically; flushed from memory on reboot.
    *   *Sticky MAC:* Learns the source MAC dynamically and automatically appends it to the running configuration file (`switchport port-security mac-address sticky`).
    *   *Violation Modes:*
        *   *Protect:* Drops unauthorized frames silently; keeps interface up; increments no counters; sends no alerts.
        *   *Restrict:* Drops unauthorized frames; increments violation counter; generates Syslog events and SNMP traps.
        *   *Shutdown:* Drops frames; increments counter; immediately disables the interface into `err-disable` state.
*   **DHCP Snooping:** Mitigates rogue DHCP servers and DHCP starvation attacks:
    *   *Trusted Ports:* Uplinks connected to authorized DHCP servers; allowed to forward DHCP Offer and ACK packets.
    *   *Untrusted Ports:* Standard client access ports; drops any incoming DHCP Offer or ACK packets.
    *   *DHCP Snooping Binding Database:* Tracks client MAC address, assigned IP, lease duration, VLAN ID, and port number.
*   **Dynamic ARP Inspection (DAI):** Defends against ARP Spoofing / ARP Poisoning attacks. Intercepts ARP packets on untrusted ports and validates their IP-to-MAC bindings against the DHCP Snooping database. Mismatched packets are dropped.
*   **IP Source Guard (IPSG):** Prevents IP address spoofing on untrusted ports by dynamically creating per-port Layer 3 ACLs based on DHCP Snooping database entries.
*   **VLAN Hopping Attacks:**
    *   *Switch Spoofing:* Attacker negotiates an 802.1Q trunk link using DTP to capture all VLAN traffic (mitigate via: `switchport mode access`).
    *   *Double Tagging:* Attacker sends frames with two 802.1Q tags matching the Native VLAN on the outer tag. The first switch strips the outer tag, and the second switch routes the frame to the target VLAN (mitigate by setting the Native VLAN to an isolated, unused VID).

### 5.4 Access Control Lists (ACLs)
*   **ACL Processing Rules:** Read top-to-bottom; stops at the first matching entry; terminates with an invisible default **implicit deny any** statement.
*   **Standard ACLs (IDs 1-99 and 1300-1999):**
    *   Inspects **Source IP address only**.
    *   *Placement Rule:* Locate as close to the **destination** as possible.
*   **Extended ACLs (IDs 100-199 and 2000-2699):**
    *   Inspects **Source IP, Destination IP, L4 Protocol (TCP/UDP/ICMP), and Port numbers**.
    *   *Placement Rule:* Locate as close to the **source** as possible to minimize unnecessary network transit.
*   **Wildcard Masks:** Inverted bitmasks where binary `0` requires an exact match, and binary `1` ignores the bit ("don't care"):
    *   Single Host (`/32`): `0.0.0.0`
    *   Class C Subnet (`/24`): `0.0.0.255`
    *   Formula: `255.255.255.255 - Subnet Mask = Wildcard Mask`.

### 5.5 Cryptography & VPN Technologies
*   **Cryptographic Core Principles:**
    *   *Symmetric Encryption:* Uses the identical shared key for encryption and decryption (e.g., AES-GCM, 3DES). Fast, low CPU overhead.
    *   *Asymmetric Encryption:* Uses a mathematically linked public and private key pair (e.g., RSA, ECC). High CPU overhead; used for digital signatures and secure key exchange.
    *   *Hashing (Data Integrity):* One-way cryptographic algorithms producing fixed-length digest outputs verifying data integrity (e.g., SHA-256).
    *   *Diffie-Hellman (DH):* Asymmetric key-agreement protocol allowing two endpoints to generate a shared symmetric key across an insecure public channel.
*   **IPsec (IP Security) Architecture:**
    *   *AH (Authentication Header - IP Protocol 51):* Provides data integrity and origin authentication; **provides no encryption**.
    *   *ESP (Encapsulating Security Payload - IP Protocol 50):* Provides encryption, data integrity, and origin authentication.
    *   *Transport Mode:* Encrypts only the L4 payload; retains the original L3 IP header (host-to-host links).
    *   *Tunnel Mode:* Encapsulates the complete original IP packet inside a new outer IP header (standard Site-to-Site VPNs).
*   **IKE (Internet Key Exchange):**
    *   *Phase 1 (IKE SA):* Authenticates endpoints and builds an encrypted management channel (uses Diffie-Hellman).
    *   *Phase 2 (IPsec SA):* Negotiates directional security associations and encryption keys to protect data plane traffic.

### 5.6 Verification CLI Commands
```text
show port-security interface <id>   ! Check violation modes (Shutdown/Restrict), status, and violation counts
show port-security address          ! Display all secured and sticky MAC entries
show ip dhcp snooping               ! Verify operational state of DHCP snooping and trusted interface list
show ip dhcp snooping binding       ! View the dynamic MAC-to-IP-to-VLAN binding database
show ip arp inspection              ! Inspect DAI operational state and packet drop/forwarding counters
show access-lists                   ! Check configured ACL lines, match counts, and hit counters
```

---

## Chapter 6: Wireless LANs (WLANs)

### 6.1 Radio Frequency (RF) & Wi-Fi Standards
*   **Wireless Spectrum Allocations:**
    *   *2.4 GHz Band:* Better obstacle penetration and coverage area, but narrow spectrum. Only 3 non-overlapping 20 MHz channels (1, 6, 11). Highly congested.
    *   *5 GHz Band:* Shorter coverage area, but wider spectrum. Offers up to 24 non-overlapping 20 MHz channels, supporting 40, 80, and 160 MHz channel bonding for high throughput.
    *   *6 GHz Band (Wi-Fi 6E / Wi-Fi 7):* Up to 1200 MHz of contiguous clean spectrum with zero legacy 802.11b/g/a/n interference. Supports up to 320 MHz wide channels.
*   **802.11 Standards Progression Table:**

| Standard | Commercial Name | Frequencies | Max Theoretical Data Rate | Modulation / Key Feature |
|---|---|---|---|---|
| **802.11b** | Legacy | 2.4 GHz | 11 Mbps | DSSS |
| **802.11a** | Legacy | 5 GHz | 54 Mbps | OFDM |
| **802.11g** | Legacy | 2.4 GHz | 54 Mbps | OFDM |
| **802.11n** | Wi-Fi 4 | 2.4 / 5 GHz | 600 Mbps | MIMO (up to 4x4) |
| **802.11ac** | Wi-Fi 5 | 5 GHz | 6.9 Gbps | 256-QAM, Downlink MU-MIMO |
| **802.11ax** | Wi-Fi 6 / 6E | 2.4 / 5 / 6 GHz | 9.6 Gbps | OFDMA, 1024-QAM, Bi-directional MU-MIMO |
| **802.11be** | Wi-Fi 7 | 2.4 / 5 / 6 GHz | 46 Gbps | 4096-QAM, 320 MHz channels, MLO |

*   **Wireless Antenna & RF Principles:**
    *   *MIMO (Multiple Input, Multiple Output):* Uses multiple antennas (2x2, 4x4) to send distinct spatial data streams simultaneously across reflections.
    *   *MU-MIMO:* Allows an AP to transmit independent spatial streams to multiple wireless stations concurrently.
    *   *Beamforming:* Coordinates antenna signal phases to focus radio frequency energy directly toward a client's location rather than broadcasting omnidirectionally.

### 6.2 WLAN Topologies & Controller Architectures
*   **SSID (Service Set Identifier):** The broadcast name identifying a wireless network.
*   **BSS (Basic Service Set) & BSSID:** An Access Point and its associated wireless stations; identified by the AP radio's Layer 2 MAC address (**BSSID**).
*   **ESS (Extended Service Set):** Two or more BSS access points connected across a common Layer 2 distribution switch network sharing the same SSID, allowing seamless client roaming.
*   **AP Deployment Models:**
    *   *Autonomous AP:* Standalone operational unit; each AP independently manages RF channels, SSIDs, security policies, and VLAN bridging locally.
    *   *Cloud-Managed AP:* Management plane offloaded to a vendor cloud dashboard (e.g., Cisco Meraki), while the data plane bridges traffic locally.
    *   *Split-MAC Architecture (Lightweight APs - LAP):* Divides wireless operations between two hardware systems:
        *   *Lightweight AP (Real-time operations):* Frame transmission, reception, beaconing, probe responses, and Layer 2 encryption handshakes.
        *   *WLC (Wireless LAN Controller):* Client authentication, central roaming logic, 802.1Q VLAN mapping, and automated Radio Resource Management (RRM).
*   **CAPWAP (Control and Provisioning of Wireless Access Points):** UDP tunnel protocol linking LAPs with the WLC:
    *   *Control Plane:* Encrypted via DTLS; transports AP management configurations (UDP port 5246).
    *   *Data Plane:* Encapsulates wireless client data packets back to the controller (UDP port 5247).

### 6.3 Wireless Security Implementations
*   **WPA2 (Wi-Fi Protected Access 2):** Uses the **AES** encryption cipher combined with **CCMP** (Counter Mode Cipher Block Chaining Message Authentication Code Protocol).
*   **WPA3 Improvements:** Replaces PSK with **SAE (Simultaneous Authentication of Equals)** based on the Dragonfly handshake, eliminating offline dictionary attacks and providing forward secrecy.
*   **Authentication Deployment Modes:**
    *   *Personal (WPA2/WPA3-PSK):* Every wireless device shares the identical pre-shared key.
    *   *Enterprise (802.1X / EAP):* Individual user authentication validated against a central **RADIUS / AAA server** (e.g., PEAP, EAP-TLS).

### 6.4 Verification CLI Commands (Cisco WLC CLI)
```text
show wlan summary               ! Display all configured WLAN profiles, SSIDs, and operational status
show ap summary                 ! View registered Lightweight Access Points, IP addresses, and models
show client summary             ! Inspect connected wireless clients, associated APs, and MAC addresses
```

---

## Chapter 7: Network Automation, Programmability & SDN

### 7.1 Modern Network Architecture & Plane Separation
*   **The Three Architectural Planes:**
    *   *Data Plane (Forwarding Plane):* Hardware ASICs, TCAM, and network interfaces responsible for forwarding, dropping, or rewriting packets and frames.
    *   *Control Plane:* Protocols and processes responsible for building forwarding tables (e.g., OSPF, BGP, STP, ARP, CAM table learning).
    *   *Management Plane:* Administrative interfaces used for device management and configuration (SSH, Console, HTTPS, SNMP, NETCONF).
*   **Software-Defined Networking (SDN):** Decouples the Control Plane from local physical hardware and centralizes it inside a centralized software controller.
    *   *Southbound APIs:* The controller's downstream interface used to configure and program physical forwarding devices (OpenFlow, NETCONF, RESTCONF).
    *   *Northbound APIs:* The controller's upstream interface exposing network topology and telemetry to business applications and automation scripts.
*   **Cisco DNA Center (Catalyst Center):** Centralized SDN controller providing intent-based networking, automated fabric deployment (SD-Access), policy enforcement, and AI-driven telemetry analytics.

### 7.2 APIs & Data Serialization Formats
*   **REST (Representational State Transfer):** Stateless, web-based API architecture running operations over HTTP/HTTPS.
*   **Standard CRUD to HTTP Methods:**
    *   **Create:** HTTP `POST` (Generates a new configuration or resource).
    *   **Read:** HTTP `GET` (Retrieves resource configuration data).
    *   **Update:** HTTP `PUT` (Replaces an existing resource completely) / HTTP `PATCH` (Applies partial modifications).
    *   **Delete:** HTTP `DELETE` (Removes an existing resource).
*   **Standard HTTP Status Codes:**
    *   `200 OK`: Request succeeded.
    *   `201 Created`: Resource successfully created.
    *   `400 Bad Request`: Client-side payload or formatting error.
    *   `401 Unauthorized`: Authentication credentials missing or invalid.
    *   `403 Forbidden`: Authenticated, but lacking administrative privileges.
    *   `404 Not Found`: Target resource URI does not exist.
    *   `500 Internal Server Error`: Server-side processing failure.
*   **JSON (JavaScript Object Notation):** Lightweight, human-readable data format:
    *   Key-value pairs separated by colons.
    *   Objects enclosed in curly braces `{}`.
    *   Arrays (lists) enclosed in square brackets `[]`.

```json
{
  "ietf-interfaces:interface": {
    "name": "GigabitEthernet0/1",
    "description": "Uplink_to_Switch",
    "type": "iana-if-type:ethernetCsmacd",
    "enabled": true,
    "ipv4": {
      "address": [
        {
          "ip": "192.168.10.1",
          "netmask": "255.255.255.0"
        }
      ]
    }
  }
}
```

*   **YAML (YAML Ain't Markup Language):** Indentation-sensitive serialization standard heavily used by configuration management tools like Ansible:

```yaml
ietf-interfaces:interface:
  name: GigabitEthernet0/1
  description: Uplink_to_Switch
  type: iana-if-type:ethernetCsmacd
  enabled: true
  ipv4:
    address:
      - ip: 192.168.10.1
        netmask: 255.255.255.0
```

*   **XML (Extensible Markup Language):** Tag-based structured data format used by traditional web services and the NETCONF protocol:

```xml
<interface xmlns="urn:ietf:params:xml:ns:yang:ietf-interfaces">
  <name>GigabitEthernet0/1</name>
  <description>Uplink_to_Switch</description>
  <enabled>true</enabled>
</interface>
```

### 7.3 Configuration Management Tools
*   **Ansible:**
    *   *Architecture:* **Agentless** (managed network devices require no software agent installed).
    *   *Control Transport:* Connects using standard SSH or REST APIs from a central Linux control node.
    *   *Playbooks:* Declarative automation playbooks written in **YAML**.
    *   *Paradigm:* Push model (configurations are pushed out on demand).
*   **Puppet:**
    *   *Architecture:* Primarily **Agent-based** (client daemon installed on managed endpoints).
    *   *Language:* Declarative Puppet DSL (Ruby-based).
    *   *Paradigm:* Pull model (agents query the central Puppet Master periodically).
*   **Chef:**
    *   *Architecture:* **Agent-based** (Chef-client agent installed on managed nodes).
    *   *Language:* Imperative Ruby-based Cookbooks and Recipes.
    *   *Paradigm:* Pull model from a centralized Chef Server.

### 7.4 Generative AI & Modern Network Operations (CCNA v1.1 Addition)
*   **Predictive AI vs. Generative AI in Networking:**
    *   *Predictive AI:* Evaluates time-series telemetry to detect anomalies, anticipate link congestion, and forecast hardware failure.
    *   *Generative AI (GenAI):* Uses Large Language Models (LLMs) to synthesize device configurations, parse unstructured Syslog and debug outputs, and generate automated diagnostic workflows.
*   **AI-Assisted Troubleshooting:** Using natural language prompts through an API to correlate complex multi-hop event logs, isolate root causes, and verify syntax against security policies before deployment.

---

---

## Appendix C: Advanced Cisco Specific Concepts & Deep-Dive Tables

### C.1 OSPF Core Packet & LSA Types
*   **OSPF Packet Types (L4 Protocol 89):**
    *   *Type 1 - Hello:* Establishes and sustains neighbor adjacencies.
    *   *Type 2 - DBD (Database Description):* Summarizes LSDB contents during Exchange state.
    *   *Type 3 - LSR (Link-State Request):* Requests specific, more recent LSAs from a peer.
    *   *Type 4 - LSU (Link-State Update):* Transports requested LSAs to neighbors (flooding).
    *   *Type 5 - LSAck (Link-State Acknowledgment):* Explicitly confirms receipt of LSUs.
*   **Key OSPF LSA Types:**
    *   *Type 1 (Router LSA):* Originated by every router; describes local links within an area.
    *   *Type 2 (Network LSA):* Originated by the DR; lists all connected routers on multi-access links.
    *   *Type 3 (Summary LSA):* Originated by ABRs; advertises inter-area routes across Area 0.
    *   *Type 4 (ASBR Summary LSA):* Originated by ABRs; advertises the path to reach an external ASBR.
    *   *Type 5 (External LSA):* Originated by ASBRs; advertises external routes redistributed into OSPF.

### C.2 Cisco Proprietary Enterprise Security & Monitoring
*   **Cisco TrustSec & Security Group Tags (SGT):** Policy architecture applying 16-bit metadata tags at ingress; enforces role-based access control inside the network fabric regardless of IP/subnet changes.
*   **Cisco Umbrella:** Cloud-delivered secure internet gateway (SIG) enforcing threat defense at the recursive DNS lookup layer.
*   **Cisco Secure Network Analytics (formerly Stealthwatch):** Ingests NetFlow/IPFIX telemetry using machine learning to detect zero-day anomalies and lateral threat movement.
*   **Cisco Catalyst Center (DNA Spaces & Assurance):**
    *   *DNA Spaces:* Cloud indoor-location service translating Wi-Fi radio RSSI telemetry into asset tracking.
    *   *Assurance (Client 360 / Device 360):* Continuous health scoring, guided issue remediation, and sensor-driven throughput validation.

---

##  Appendix D: IPv6 Well-Known Multicast Reference

| Multicast Address | Scope & Description |
|---|---|
| `ff02::1` | All IPv6 Nodes on the local link |
| `ff02::2` | All IPv6 Routers on the local link |
| `ff02::5` | OSPFv3 All Routers |
| `ff02::6` | OSPFv3 Designated Routers (DR / BDR) |
| `ff02::9` | RIPng Routers |
| `ff02::a` | EIGRP for IPv6 Routers |
| `ff02::1:2` | All DHCPv6 Agents (Relay / Server) on the local link |
| `ff02::1:ffXX:XXXX` | Solicited-Node Multicast (used for NDP address resolution and DAD) |