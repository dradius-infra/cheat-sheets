# CCNA 200-301 | Domain 1: Network Fundamentals (100% Master Deck)

---

## High-Yield Anchors

> * **Frame Boundaries:** Min = 64 Bytes | Max = 1518 Bytes (1522B with 802.1Q).
> * **MSS Formula:** $1500 - 20\text{ (IP)} - 20\text{ (TCP)} = \mathbf{1460\text{ Bytes}}$.
> * **Auto-Neg Mismatch Trap:** Speed is detected electrically; Duplex **fails $\rightarrow$ defaults to Half-Duplex** (Late Collisions!).
> * **IPv6 Multicast Quick IDs:** `ff02::1` (All Nodes) | `ff02::2` (All Routers) | `ff02::1:ffXX:XXXX` (Solicited-Node).
> * **EUI-64 Rule:** Split MAC in half $\rightarrow$ Insert `FF:FE` $\rightarrow$ Flip the **7th bit** ($\pm 2$ on 2nd hex digit).
> 
> 

---

## 1.1 Network Architecture, Topologies, Models & Planes

### Network Architectural Planes

| Plane | Core Role | Key Protocols / Tables | Hardware Level |
| --- | --- | --- | --- |
| **Data Plane** | Moves user traffic ingress $\rightarrow$ egress | FIB, Adjacency Table, Packet Filter/ACL, NAT | Hardware (ASICs, TCAM, CEF) |
| **Control Plane** | Populates Data Plane forwarding tables | OSPF, EIGRP, BGP, STP, ARP, NDP, CDP/LLDP | CPU / Software |
| **Management Plane** | Device access, configuration & logging | SSH, Telnet, SNMPv2c/v3, Syslog, NTP, RESTCONF | CPU / Software |

```
       +------------------------------------------------+
       |   Management Plane (SSH, SNMP, Syslog, GUI)   |
       +------------------------------------------------+
       +------------------------------------------------+
       |     Control Plane (OSPF, EIGRP, STP, ARP)      |
       +------------------------------------------------+
                               | Populates FIB & TCAM
                               v
       +------------------------------------------------+
       | Data Plane (ASICs, TCAM, CEF FIB / Adjacency)  |
       +------------------------------------------------+

```

### In-Band vs. Out-of-Band (OOB) Management

* **In-Band Management:** Traverses the standard production data link using IP addresses via VTY lines (SSH/Telnet). If the network links or interfaces fail, administrative access drops.
* **Out-of-Band (OOB) Management:** Dedicated, isolated physical channel isolated from production data traffic (Console RJ-45/micro-USB, AUX port, or dedicated `GigabitEthernet0/0` management port). Operates during complete network outages.
* **Serial Console RS-232 COM Defaults:** **9600-8-N-1**
* Speed: **9600 bps**
* Data bits: **8**
* Parity: **None**
* Stop bits: **1**
* Flow control: **None**



### SDN & Controller-Based Architecture

* **Decoupling:** Control Plane intelligence is removed from local physical switches/routers and centralized into an SDN Controller (e.g., Cisco Catalyst Center / DNA Center). Local physical network nodes retain only the Data Plane.
* **Southbound APIs (SBIs):** Connect the Controller down to physical network devices.
* *Protocols:* OpenFlow, NETCONF (SSH-based / XML), RESTCONF (HTTP-based / JSON or XML), OnePK, gRPC.


* **Northbound APIs (NBIs):** Connect the Controller up to management applications and automation scripts (typically RESTful APIs / JSON payloads).

### Campus & Data Center Models

* **Three-Tier Campus Hierarchical Model:**
* **Core Layer:** High-speed switching fabric optimized exclusively for raw packet forwarding speed. Strictly avoids CPU-intensive processing (ACL filtering, deep inspection, SVI VLAN termination).
* **Distribution Layer:** Policy-based boundaries, routing, packet filtering (ACLs), QoS enforcement, VLAN termination (SVIs), and broadcast domain boundaries.
* **Access Layer:** Direct network connectivity endpoints for client PCs, IP phones, printers, and WAPs. Enforces port security, 802.1X, PoE, and STP edge features (PortFast/BPDU Guard).


* **Two-Tier (Collapsed Core) Model:**
* Consolidates Core and Distribution layers into a single layer on a pair of multilayer switches.
* Designed for small-to-medium enterprise networks to minimize hardware costs and operational overhead while retaining redundancy.


* **Spine-Leaf Architecture (Data Center Fabric):**
* Purpose-built for high-volume **East-West** (server-to-server) data center traffic flows.
* **Spine Switches:** Non-blocking high-speed backbone. Spines connect **exclusively** to Leaf switches (never to other Spines).
* **Leaf Switches:** Connect to every Spine switch and aggregate end nodes (bare-metal servers, hypervisors, storage arrays). Leaf switches never connect to other Leaf switches.
* **Deterministic Latency:** Every server-to-server traffic path spans precisely two hops: Leaf $\rightarrow$ Spine $\rightarrow$ Leaf.



### WAN Topologies & SOHO

* **Point-to-Point (P2P):** Dedicated serial line, leased line, or Layer 2 tunnel connecting two distinct endpoints.
* **Hub-and-Spoke (Star):** Multiple remote spoke sites communicate with a central hub node. Spoke-to-spoke transit requires hair-pinning through the central hub.
* **Full Mesh:** Every node maintains a direct physical or logical connection to every other node. Total links formula:

$$\text{Links} = \frac{N(N-1)}{2}$$

* **Partial Mesh:** Selective redundant connections established between mission-critical nodes to balance uptime against link costs.
* **SOHO (Small Office / Home Office) Network Architecture:** Integrated all-in-one edge hardware (Integrated Services Router) uniting WAN routing, PPPoE/DHCP client, stateful firewalling, NAT overload (PAT), switching, and wireless connectivity into a unified deployment.

### On-Premises, Colocation & Cloud Deployment

* **CapEx (Capital Expenditure):** Upfront purchases of physical hardware assets, datacenters, cabling, and cooling systems. Depreciates over time (characteristic of On-Premises deployments).
* **OpEx (Operational Expenditure):** Ongoing operational costs, monthly subscriptions, and pay-as-you-go consumption (characteristic of Cloud Computing).
* **Physical Infrastructure Strategies:**
* **On-Premises:** Complete ownership and maintenance of hardware, floor space, electrical systems, and cooling (100% CapEx driven).
* **Colocation (Co-Lo):** The enterprise rents physical rack space, power, bandwidth, and environmental cooling inside a shared commercial facility, while owning and configuring its own physical server/switch hardware.
* **Cloud:** Fully abstracting hardware and facilities behind an API/dashboard billing model.


* **Cloud Service Models:**
* **IaaS (Infrastructure as a Service):** Cloud vendor provisions compute, virtualization, storage, and networking; user manages OS, patches, runtimes, middleware, and applications (e.g., AWS EC2, Azure VMs).
* **PaaS (Platform as a Service):** Vendor manages hardware, OS, networking, runtimes, and auto-scaling; user supplies and deploys application source code (e.g., AWS Elastic Beanstalk, Google App Engine).
* **SaaS (Software as a Service):** Fully vendor-managed application delivery. Customer interacts strictly as an end user (e.g., Microsoft 365, Google Workspace, Salesforce).


* **Cloud Deployment Models:**
* **Public Cloud:** Multi-tenant shared infrastructure managed by hyperscalers (AWS, Azure, GCP).
* **Private Cloud:** Dedicated single-tenant infrastructure configured on-premises or via private hosted datacenters.
* **Hybrid Cloud:** Seamless integration bridging on-premises enterprise infrastructure with public cloud instances via IPsec VPN or direct private interconnects (AWS DirectConnect, Azure ExpressRoute).



### Server Virtualization, Containers & NFV

* **Hypervisors:**
* **Type 1 (Bare-Metal):** Installed directly on physical bare-metal hardware without a host OS; delivers maximum performance, low latency, and enterprise scalability (VMware ESXi, Cisco UCS, Microsoft Hyper-V, KVM).
* **Type 2 (Hosted):** Runs as software on top of an existing host OS; introduces virtualization overhead and latency (VMware Workstation, Oracle VirtualBox).


* **Virtual Machines (VMs) vs Containers:**
* *VM:* Fully isolated instance bundling virtual hardware, kernel, drivers, and a complete Guest OS managed by a hypervisor.
* *Container:* Lightweight execution environment (e.g., Docker) sharing the host OS kernel and isolating user-space processes, binaries, and libraries; faster deployment and smaller footprint than VMs.


* **Virtual Switching (vSwitch) Internal Data Plane:**
* A software-based Layer 2 forwarding entity internal to hypervisors bridging traffic between VM vNICs and physical network adapters (pNICs).
* **East-West VM-to-VM Switching:** When two VMs reside on the **same physical hypervisor** in the same VLAN, frames pass **directly across the host's RAM memory** via the software vSwitch. The frame **never** exits onto the physical Ethernet cable.
* *EST (External Switch Tagging):* Tagging occurs on the external physical switch port.
* *VST (Virtual Switch Tagging):* The vSwitch performs 802.1Q tagging/VLAN trunking before frames hit the physical link.
* *VGT (Virtual Guest Tagging):* The VM / Guest OS itself receives, generates, and processes 802.1Q tagged frames directly.


* **Network Function Virtualization (NFV):** Replaces dedicated hardware appliances (firewalls, routers, load balancers) with virtual instances known as **VNFs (Virtual Network Functions)** (e.g., Cisco ASAv, Cisco CSR1000v, Cisco Catalyst 8000v).
* **VRF (Virtual Routing and Forwarding):** IP network virtualization technique allowing multiple, completely isolated routing tables to coexist simultaneously on a single router or L3 switch; enables overlapping IP address spaces without leaking routes.

### Network Device Roles & Security Functions

* **Routers:** Layer 3 forwarding devices interconnecting heterogeneous network boundaries, terminating broadcast domains, and making route determinations using IP routing tables.
* **Layer 2 Switches:** Hardware-based forwarding devices building local collision-free domains and forwarding Ethernet frames based strictly on dynamic MAC address tables.
* **Multilayer (L3) Switches:** High-density switching hardware integrating ASIC-accelerated Layer 3 routing engines (CEF) with Layer 2 switching capabilities (SVIs and routed ports).
* **Next-Generation Firewalls (NGFW):** Stateful inspection platforms extending inspection up to Layer 7 (Application layer), integrating Application Visibility and Control (AVC), threat intelligence, and SSL/TLS deep packet inspection/decryption.
* **Next-Generation Intrusion Prevention Systems (NGIPS):** Inline threat detection and mitigation engines performing real-time Deep Packet Inspection (DPI) and behavioral heuristics to stop exploits.
* **Controllers:** Centralized control plane engines decoupling management from data forwarding (e.g., Cisco Catalyst Center for SDN campus management, and Wireless LAN Controllers for central RF/AP policy control).
* **Generative AI in Network Operations (Cisco AI Assistant):** Integration of LLMs and generative AI tools within management dashboards to interpret telemetry, query device states using natural language, auto-generate standard CLI configurations, and accelerate root-cause troubleshooting (RCA).

### Cisco Device Internal Architecture & Boot Process

* **Memory Types:**
* **ROM (Read-Only Memory):** Contains microcode for POST (Power-On Self-Test), Bootstrap loader, and ROMMON (low-level recovery mini-OS).
* **Flash Memory:** Non-volatile memory (persists through reboots). Stores Cisco IOS / IOS-XE system images.
* **NVRAM (Non-Volatile RAM):** Non-volatile memory. Stores the **startup-config** and the **Configuration Register**.
* **RAM (Random Access Memory):** Volatile memory (lost upon power down/reboot). Stores the active **running-config**, routing tables, ARP cache, MAC address tables, and packet buffers.


* **Boot Sequence:**
1. **POST** executes from ROM (validates CPU, RAM, and interface hardware).
2. **Bootstrap** loader loads from ROM.
3. Bootstrap reads the **Configuration Register** (default: `0x2102`):
* *0x2102:* Normal boot (loads IOS from Flash and startup-config from NVRAM).
* *0x2142:* Password Recovery Mode (bypasses startup-config in NVRAM and boots with an empty running-config).


4. Cisco **IOS image** is located and loaded from Flash into RAM.
5. The **startup-config** in NVRAM is parsed and copied into RAM as the active **running-config**. If no startup-config exists, the interactive Setup Wizard (`System Configuration Dialog`) starts.


* **Cisco CLI Modes & Contexts:**
* `Router>`: **User EXEC Mode** (basic read-only diagnostic checks, ping, traceroute).
* `Router#`: **Privileged EXEC Mode** (full monitoring access, show commands, debugs, reload). Accessed via `enable`.
* `Router(config)#`: **Global Configuration Mode** (modifies system-wide parameters, hostname, routing). Accessed via `configure terminal`.
* `Router(config-if)#`: **Interface Configuration Mode** (IP addresses, speed, duplex, shutdown/no shutdown).
* `Router(config-line)#`: **Line Configuration Mode** (Console, AUX, and VTY/SSH terminal access lines).
* `Router(config-router)#`: **Routing Protocol Configuration Mode** (OSPF, EIGRP parameters).


* **CLI Operational Shortcuts & Keystrokes:**
* `Tab`: Auto-complete partial command string.
* `Ctrl + A`: Move cursor straight to the **beginning** of the command line.
* `Ctrl + E`: Move cursor straight to the **end** of the line.
* `Ctrl + U` / `Ctrl + X`: Erase the entire command line back to the prompt.
* `Ctrl + C` or `end`: Immediately pop out from any sub-configuration mode straight to Privileged EXEC (`#`).
* `exit`: Step backwards one configuration level.
* `Ctrl + Z`: Functionally identical to `end`.
* `Ctrl + Shift + 6`: Cisco break sequence; instantly terminates hanging pings, traceroutes, or unwanted DNS lookups.
* `no ip domain-lookup`: Disables automatic broadcast DNS resolution on typo commands, stopping the CLI from freezing for 30 seconds.


* **Cisco IOS Help System & Error Parsing:**
* `c?`: Displays all commands that **begin with the letter "c"** (no space).
* `clock ?`: Displays the **next required/optional parameters** for the `clock` command (with space).
* `% Incomplete command`: A required parameter was omitted before pressing Enter.
* `% Ambiguous command`: Insufficient characters were entered to identify a unique command (e.g., typing `d` when `dir` and `debug` are both valid).
* `% Invalid input detected at '^' marker`: The caret symbol (`^`) points directly to the **first unrecognized character** in the string.



### Network Performance Metrics

* **Latency (Delay):** The time required for a packet to traverse the transmission path from source to destination.
* *Components:* Propagation delay (transit speed through medium), Serialization delay (time to encode bits onto physical wire), Queuing delay (buffer wait time during congestion), Processing delay (time spent parsing headers and route lookups).


* **Jitter:** The statistical variation in packet transit delay over time; critical threshold for real-time traffic (VoIP, video conference) requiring jitter buffers (target threshold $< 30\text{ ms}$).
* **Packet Loss:** Percentage of packets dropped in transit due to buffer congestion, link errors, or queue drops; triggers TCP dynamic window throttling and retransmissions.

---

## 1.2 Physical Media, Cabling, Ethernet Standards & Diagnostics

### Media Access, Duplex & Switching Forwarding Methods

* **Half-Duplex (Shared Ethernet):** Segments where nodes share transmission capacity (e.g., legacy hubs); only one node can transmit at any given time.
* **CSMA/CD (Carrier Sense Multiple Access with Collision Detection):**
1. Station listens to the wire before transmitting (Carrier Sense).
2. If wire is idle, station begins transmitting.
3. If two stations transmit concurrently, a collision occurs.
4. Transmitting stations detect collision and send a **Jam Signal** to notify the segment.
5. Nodes invoke a random **Truncated Binary Exponential Backoff Timer** before retrying transmission.


* **Full-Duplex (Point-to-Point):** Dedicated transmit and receive wire pairs between two endpoints (e.g., Switch to Switch, Switch to Host); collisions are **physically impossible**, disabling CSMA/CD requirements entirely.
* **Switch Forwarding Architectures:**
* **Store-and-Forward:** Switch buffers the entire frame, verifies FCS/CRC checksum for errors. If valid, forwards; if corrupt, drops. Highest latency, zero corrupt propagation.
* **Cut-Through:** Reads only the first 6 bytes (Destination MAC) and forwards immediately before the rest of the frame arrives. Lowest latency, but blindly forwards corrupt frames and runts.
* **Fragment-Free:** Reads the first **64 bytes** (minimum collision window threshold) before forwarding, discarding runts and collision fragments.



### Interface Line States & Diagnostics

* **Line States:**
* `Status: administratively down / Protocol: down`: Interface is manually disabled using the `shutdown` command.
* `Status: down / Protocol: down`: Layer 1 physical failure (unplugged cable, cut fiber, remote interface shut down, dead transceiver).
* `Status: up / Protocol: down`: Layer 2 data link issue (encapsulation mismatch, keepalive failure, missing serial clock rate, native VLAN mismatch, duplex mismatch).
* `Status: up / Protocol: up`: Fully operational Layer 1 physical and Layer 2 link-state.


* **Counters & Troubleshooting (`show interfaces <id>`):**
* **Late Collisions:** Collisions occurring **after the first 64 bytes (512 bits)** of an Ethernet frame transmission. Almost always indicates a **Duplex Mismatch** (one side Full, the other Half) or a physical cable run exceeding the 100-meter specification.
* **Auto-Negotiation Failure Rule:** If one switch port is hardcoded to `speed 100 / duplex full` and the opposite side remains set to `auto`: the auto side senses speed (100 Mbps) via electrical pulses, but **fails** duplex negotiation, falling back to **Half Duplex** (standard IEEE default). Result: duplex mismatch, late collisions on the Half-duplex side, and CRC errors on the Full-duplex side.
* **Runts:** Frames smaller than the minimum 64-byte Ethernet standard, typically caused by collisions or electrical noise.
* **Giants:** Frames exceeding the standard 1518-byte MTU (or 1522 bytes with 802.1Q) received on non-jumbo interfaces.
* **CRC / FCS Errors:** Frames failing checksum validation, identifying faulty copper termination, EMI interference, or bad transceivers.
* **Input Drops:** The interface input queue/buffer memory is full; the switch engine cannot process packets fast enough.
* **Output Drops:** The egress interface queue buffer is full due to link congestion.
* **Interface Resets:** Indicates the interface subsystem hung and was restarted by the system, typically caused by hardware failure or link flapping.



### Copper Cabling (Twisted Pair)

* **UTP vs. STP Construction:**
* **UTP (Unshielded Twisted Pair):** No metallic foil shielding. Relies strictly on the geometric **cancellation effect** (twisting pairs tightly together cancels out electromagnetic noise and crosstalk).
* **STP (Shielded Twisted Pair):** Encased in outer foil shielding and braided metal around individual pairs. Used in high-EMI industrial/manufacturing plants; requires grounding.


* **Categories:**
* **Cat5e:** Up to 1 Gbps at 100 MHz, max run distance 100 meters.
* **Cat6:** 1 Gbps up to 100m, 10 Gbps supported up to 55m (250 MHz).
* **Cat6a:** Full 10 Gbps support up to the maximum 100m distance (500 MHz); enhanced shielding against alien crosstalk.


* **Pinouts & Cable Types:**
* **T568A Order:** White/Green, Green, White/Orange, Blue, White/Blue, Orange, White/Brown, Brown.
* **T568B Order:** White/Orange, Orange, White/Green, Blue, White/Blue, Green, White/Brown, Brown.
* **Straight-Through Cable:** Both ends terminated using the same standard (T568B to T568B or T568A to T568A); connects differing device types (Switch to Host, Switch to Router, Hub to PC).
* **Crossover Cable:** One end wired to T568A, opposite end wired to T568B; connects like devices without auto-negotiation (Switch to Switch, PC to PC, Router to Router, **Router to PC**).
* *Fast Ethernet (10/100 Mbps) Crossover Pinout:* Swaps Transmit (TX) and Receive (RX) wire pairs while keeping remaining pins straight-through:
* Pin 1 $\leftrightarrow$ Pin 3 (White/Orange $\leftrightarrow$ White/Green)
* Pin 2 $\leftrightarrow$ Pin 6 (Orange $\leftrightarrow$ Green)
* Pins 4, 5, 7, 8 remain straight-through.


* *Gigabit Ethernet (1000BASE-T) Crossover Pinout:* Utilizes all four pairs; crosses $1 \leftrightarrow 3$, $2 \leftrightarrow 6$, $4 \leftrightarrow 7$, and $5 \leftrightarrow 8$.
* **Auto-MDIX (Automatic Medium-Dependent Interface Crossover):** NIC firmware algorithm that automatically detects cable transmission pairs and corrects internal pinouts dynamically (requires auto-negotiation enabled).
* **Rollover (Console) Cable:** RJ45-to-DB9/USB pinout reversing pin order completely ($1 \leftrightarrow 8, 2 \leftrightarrow 7, 3 \leftrightarrow 6, 4 \leftrightarrow 5$); dedicated to Out-of-Band (OOB) serial terminal access.



### Fiber Optic Cabling & Optical Transceivers

* **Single-Mode Fiber (SMF):**
* Extremely thin silica core (~9 µm).
* Light source: **Laser Diode** at wavelengths of 1310 nm and 1550 nm.
* Eliminates modal dispersion (light travels in a single straight path).
* Used for long-haul and ISP backbone spans (tens of kilometers).


* **Multi-Mode Fiber (MMF):**
* Wider core (50–62.5 µm).
* Light source: **LED or VCSEL** at wavelengths of 850 nm and 1300 nm.
* Light rays bounce at variable angles causing **modal dispersion**; restricted to local campus and datacenter runs (typically up to 300–550m).


* **Optical Phenomena & Multiplexing:**
* **Attenuation:** Natural loss of light intensity over cable length (measured in dB/km).
* **WDM (Wavelength-Division Multiplexing):** Transmits multiple data streams across a single physical fiber strand simultaneously using distinct optical wavelengths (colors of light). Split into CWDM (Coarse) and DWDM (Dense).


* **Common Optical Connectors:**
* **LC (Lucent Connector):** Compact, high-density push-pull latching connector (standard for modern SFP/SFP+ modules).
* **SC (Subscriber Connector):** Square push-pull latching connector.
* **ST (Straight Tip):** Legacy cylindrical bayonet twist-lock connector.
* **MPO / MTP (Multi-Fiber Push-On):** Ribbon connector terminating 12 to 24 fiber strands simultaneously in high-density 40G/100G QSFP aggregation links.


* **Pluggable Transceivers:** SFP (1 Gbps), SFP+ (10 Gbps), QSFP+ (40 Gbps), QSFP28 (100 Gbps).
* **Official Ethernet Standards Reference:**

| IEEE Standard | Common Name | Media Type | Max Distance | Operating Wavelength |
| --- | --- | --- | --- | --- |
| **802.3u** | 100BASE-TX | Cat5 UTP (2 pairs) | 100 m | - |
| **802.3ab** | 1000BASE-T | Cat5e/6 UTP (4 pairs) | 100 m | - |
| **802.3an** | 10GBASE-T | Cat6a UTP (4 pairs) | 100 m (55m on Cat6) | - |
| **802.3z** | 1000BASE-SX | MMF (Multi-Mode) | 220 m – 550 m | 850 nm |
| **802.3z** | 1000BASE-LX | SMF / MMF | 5 km (SMF) / 550m (MMF) | 1310 nm |
| **802.3ae** | 10GBASE-SR | MMF (Multi-Mode) | 300 m – 400 m | 850 nm |
| **802.3ae** | 10GBASE-LR | SMF (Single-Mode) | 10 km | 1310 nm |
| **802.3ae** | 10GBASE-ER | SMF (Single-Mode) | 40 km | 1550 nm |

### Power over Ethernet (PoE - IEEE Standards & Wire Allocations)

| IEEE Standard | Marketing Name | Power at PSE | Power at PD | Wire Pairs Used | Wire Allocation Detail |
| --- | --- | --- | --- | --- | --- |
| **802.3af (Type 1)** | PoE | 15.4 W | 12.95 W | **2 Pairs** | Alt A (Pins 1,2,3,6) or Alt B (Pins 4,5,7,8) |
| **802.3at (Type 2)** | PoE+ | 30.0 W | 25.5 W | **2 Pairs** | Alt A (Pins 1,2,3,6) or Alt B (Pins 4,5,7,8) |
| **802.3bt (Type 3)** | PoE++ (4PPoE) | 60.0 W | 51.0 W | **2 or 4 Pairs** | Up to 60W delivery |
| **802.3bt (Type 4)** | PoE++ (4PPoE) | 90–100 W | 71.3 W | **4 Pairs (Mandatory)** | Requires all 8 wires to prevent conductor overheating |

* **PoE Dynamic Negotiation Protocols:**
* Initial detection measures physical resistance on the wire loop.
* Fine-grained wattage negotiation occurs dynamically via **CDP** (Cisco proprietary) or **LLDP-MED** (open standard).


* **PoE Terminology:**
* **PSE (Power Sourcing Equipment):** The switch or power injector delivering PoE.
* **PD (Powered Device):** The end system consuming power (IP Phone, AP, IP Camera).



---

## 1.3 Wireless Principles, Wi-Fi Generations & RF Architecture

### Wi-Fi Standards Evolution (IEEE 802.11)

| Generation | IEEE Standard | Operating Frequency | Max Theoretical Data Rate | Key Features |
| --- | --- | --- | --- | --- |
| **Legacy** | 802.11b | 2.4 GHz | 11 Mbps | DSSS modulation |
| **Legacy** | 802.11a | 5 GHz | 54 Mbps | OFDM modulation, clean spectrum |
| **Legacy** | 802.11g | 2.4 GHz | 54 Mbps | OFDM modulation, backward-compatible with 11b |
| **Wi-Fi 4** | 802.11n | 2.4 GHz & 5 GHz | 600 Mbps | MIMO (up to 4x4), 40 MHz channel bonding |
| **Wi-Fi 5** | 802.11ac | 5 GHz | 6.93 Gbps | DL MU-MIMO, 80/160 MHz channels, 256-QAM |
| **Wi-Fi 6** | 802.11ax | 2.4 GHz & 5 GHz | 9.6 Gbps | OFDMA, DL & UL MU-MIMO, 1024-QAM, BSS Color, TWT |
| **Wi-Fi 6E** | 802.11ax | 2.4, 5 & **6 GHz** | 9.6 Gbps | 6 GHz spectrum expansion (Greenfield, No DFS) |
| **Wi-Fi 7** | 802.11be | 2.4, 5 & 6 GHz | ~46 Gbps | 320 MHz channels, 4096-QAM, MLO (Multi-Link Operation) |

* **MIMO (Multiple-Input Multiple-Output):** Transmitting and receiving multiple spatial streams across multiple antennas concurrently.
* **MU-MIMO (Multi-User MIMO):** Transmitting spatial streams to multiple clients simultaneously:
* *802.11ac (Wave 2):* Downlink MU-MIMO only.
* *802.11ax:* Both Downlink and Uplink MU-MIMO.


* **OFDMA (Orthogonal Frequency Division Multiple Access):** Subdivides an RF channel into small subcarriers called Resource Units (RUs), allowing parallel transmissions to multiple clients simultaneously with low latency.

### Frequencies, Spectrum & Channel Bonding Traps

* **2.4 GHz:**
* 11 channels (North America) / 13 channels (ETSI Europe).
* 20 MHz channel bandwidth (requires 25 MHz separation to prevent overlap).
* **Non-overlapping channels:** **1, 6, and 11**.
* Greater range and obstacle penetration; highly susceptible to interference (microwave ovens, Bluetooth, legacy networks).
* **Channel Bonding Trap (2.4 GHz):** Bonding two 20 MHz channels into a single **40 MHz channel** is strongly avoided in enterprise networks. It consumes nearly the entire band, leaving zero space for clean channel reuse and causing severe Co-Channel Interference (CCI).


* **5 GHz:**
* 24+ non-overlapping 20 MHz channels (UNII bands).
* Shorter range and lower wall penetration, but vastly superior capacity and throughput.
* Supports channel bonding reliably: **40 MHz, 80 MHz, and 160 MHz** channel widths.
* **DFS (Dynamic Frequency Selection):** Shares spectrum with military and weather radar. If an AP detects radar pulses, it must immediately vacate and switch channels.


* **6 GHz (Wi-Fi 6E / Wi-Fi 7):**
* Up to 1200 MHz of contiguous spectrum (5.925 GHz to 7.125 GHz).
* Provides up to fourteen 80 MHz channels or seven 160 MHz channels.
* **Greenfield:** Devoid of legacy 802.11b/a/g/n/ac clients and entirely free from radar DFS interruptions.



### Antennas, RF Fundamentals & Environmental Interference

* **Antenna Types & Radiation Patterns:**
* **Omnidirectional:** Radiates $360^\circ$ horizontally in a donut/torus shape. Standard for indoor ceiling mounts.
* **Directional (Patch, Yagi, Dish):** Focuses RF energy into a narrow beam/cone. High gain across long distances (warehouse aisles, sports stadiums, point-to-point outdoor bridges).


* **RF Metrics:**
* **Frequency (Hz):** Number of RF cycles completed per second.
* **Wavelength ($\lambda$):** Inversely proportional to frequency ($\lambda = c / f$). Higher frequency $\rightarrow$ shorter wavelength $\rightarrow$ reduced ability to penetrate dense obstacles.
* **Signal Strength / Power (RSSI):** Received RF power measured in negative dBm:
* *-30 dBm:* Excellent signal (adjacent to AP).
* *-67 dBm:* Minimum acceptable threshold for real-time voice/video (VoWLAN).
* *-80 dBm or worse:* Poor connection, packet drops, cell edge disconnects.


* **Noise Floor:** Ambient background RF noise (typically between -90 dBm and -100 dBm).
* **SNR (Signal-to-Noise Ratio):** $\text{SNR (dB)} = \text{Signal (dBm)} - \text{Noise Floor (dBm)}$. Higher SNR delivers higher data rates and lower error rates (recommended $\ge 25\text{ dB}$ for voice/video).


* **Interference Types:**
* **Co-Channel Interference (CCI):** APs on the exact same channel share airtime and delay transmissions via CSMA/CA backoff timers.
* **Adjacent Channel Interference (ACI):** APs on partially overlapping channels bleed energy into adjacent frequencies, corrupting frames.


* **RF Propagation Effects:**
* **Absorption:** RF wave energy is converted into heat upon traversing materials (water, human bodies, drywall, solid concrete).
* **Reflection:** Signal bounces off large reflective surfaces (metal, mirrors), causing multipath interference.
* **Refraction:** Bending of the RF wave as it passes through media of differing densities (glass, acrylic).
* **Scattering:** Diffusion of the RF signal into unpredictable paths when hitting irregular surfaces (dust, heavy foliage, rain).



### Wireless Topologies & Connection Handshake

* **BSS (Basic Service Set):** A single AP servicing associated wireless clients.
* **BSSID:** The Layer 2 MAC address of the AP radio servicing that specific BSS.
* **BSA (Basic Service Area):** The physical RF coverage footprint of a BSS.
* **ESS (Extended Service Set):** Multiple BSS cells interconnected across a common Layer 2 distribution system sharing the same SSID to allow seamless client roaming.
* **SSID (Service Set Identifier):** Human-readable alphanumeric string advertising network presence (up to 32 characters).
* **IBSS (Independent BSS):** Ad-hoc direct client-to-client wireless network without an AP.
* **Workgroup Bridge (WGB):** Cisco AP operating as an RF client to bridge wired Ethernet devices into a wireless network.
* **Wireless Client Connection Order (Association Process):**
1. **Discovery:** Client scans channels actively (sending Probe Requests) or passively (listening for periodic AP Beacons).
2. **802.11 Authentication:** Open System authentication exchange (two management frames confirming identity capability).
3. **802.11 Association:** Client sends Association Request; AP replies with Association Response reserving radio buffers.
4. **Security Handshake:** If WPA2/WPA3 is enabled, client executes the 4-Way Handshake (EAPOL) to generate session encryption keys (PTK).



### Wireless Encryption & Security

* **WEP:** Deprecated 64/128-bit RC4 encryption with static IVs; easily cracked within minutes.
* **WPA:** Temporal Key Integrity Protocol (TKIP) with dynamic keys; interim solution.
* **WPA2:** Advanced Encryption Standard (AES) coupled with Counter Mode Cipher Block Chaining Message Authentication Code Protocol (CCMP); standard enterprise deployment.
* **WPA3:**
* Simultaneous Authentication of Equals (SAE) resistant to offline dictionary attacks in WPA3-Personal.
* Enforces Protected Management Frames (PMF - 802.11w).
* WPA3-Enterprise incorporates a 192-bit cryptographic suite (CNSA).



### AP Modes, Split-MAC & WLC Internal Interfaces

* **Autonomous (Fat AP):** Self-contained AP configuration where RF management, QoS, security policies, and 802.11 frames are terminated locally on the AP.
* **Lightweight AP (LAP) & Split-MAC Architecture:** Divides MAC functions between the AP and a centralized WLC:

| MAC Function | LAP (Real-Time MAC) | WLC (Management MAC) |
| --- | --- | --- |
| **Beacon generation & Probe responses** | **Yes** | No |
| **Packet buffering & Layer 2 802.11 ACKs** | **Yes** | No |
| **Real-time frame encryption/decryption** | **Yes** | No |
| **802.11 Client Association / Re-association** | No | **Yes** |
| **802.11 User Authentication (802.1X/EAP)** | No | **Yes** |
| **Frame Translation (802.11 to 802.3 Ethernet)** | No | **Yes** (in Local mode) |
| **Radio Resource Management (RRM - Dynamic RF/Power)** | No | **Yes** |

* **CAPWAP Protocol:**
* **Control Plane:** UDP Port **5246** (always encrypted with DTLS).
* **Data Plane:** UDP Port **5247** (transports client data payloads; optional DTLS encryption).


* **AP Operational Modes:**
* **Local Mode (Default):** Dedicated client-serving mode; all client data and control traffic are encapsulated in CAPWAP and centrally switched through the WLC.
* **FlexConnect Mode:** Optimized for branch offices across WAN links; locally switches client data traffic on the branch switch (VLAN mapping) if the WAN CAPWAP connection to the WLC drops.
* **Bridge / Mesh Mode:** APs link to each other wirelessly across outdoor/campus locations without physical Ethernet cables (Point-to-Multipoint bridging).
* **Monitor Mode:** AP radio dedicates full time to RF spectrum monitoring, rogue detection, and IDS/IPS location tracking; serves no clients.
* **Sniffer Mode:** Captures raw 802.11 frames on a specified channel and streams them to a packet analyzer (e.g., Wireshark).
* **Rogue Detector Mode:** AP radio disabled; listens solely to wired network ARP/MAC broadcasts to correlate and identify unauthorized rogue devices present on both wired and wireless segments.


* **WLC Logical Interface Types & LAG Support:**
* **Management Interface:** Primary static IPv4/IPv6 interface used for in-band management, SSH, GUI, AAA authentication, and CAPWAP tunnel termination.
* **Virtual Interface:** Assigned a fictitious IP (often `192.0.2.1`). Supports mobility management, guest web authentication redirection, and internal DHCP relay.
* **Dynamic Interfaces:** User-created interfaces mapping specific WLAN SSIDs to individual distribution VLANs.
* **Service Port Interface:** Dedicated physical out-of-band management interface routed to an isolated network segment.
* **WLC Port LAG (Link Aggregation):** Bundles all physical distribution system ports into a **single logical EtherChannel**. Cisco architectural rule: The WLC supports only **one single LAG bundle** across its distribution ports (multiple separate EtherChannels are unsupported), and the upstream switch must have channel-group mode statically set to `on` (no LACP/PAgP negotiation in classic AireOS).



---

## 1.4 Layer 2 Switching, Framing & Addressing

### Models, PDU Terminology & Encapsulation

* **OSI vs TCP/IP Models:**

| OSI Layer | OSI Layer Name | TCP/IP Layer | PDU Name | Examples |
| --- | --- | --- | --- | --- |
| **7** | Application | **Application** | Data | HTTP, HTTPS, DNS, DHCP, SSH, FTP, NTP |
| **6** | Presentation | ^ | Data | SSL/TLS, ASCII, JPEG, MPEG (Formatting/Encryption) |
| **5** | Session | ^ | Data | RPC, NetBIOS, SIP (Session control/keepalive) |
| **4** | Transport | **Transport** | **Segment** (TCP) / **Datagram** (UDP) | TCP, UDP (Port numbers, flow control, windowing) |
| **3** | Network | **Internet** | **Packet** | IPv4, IPv6, ICMP, IPsec, OSPF, EIGRP |
| **2** | Data Link | **Network Access** | **Frame** | Ethernet (802.3), Wi-Fi (802.11), PPP, HDLC, MAC |
| **1** | Physical | ^ | **Bits** | UTP copper, Fiber optic pulses, RF waves |

### Ethernet Frame Breakdown (IEEE 802.3)

```
+----------+-----+----------+----------+-----------+----------------+---------+
| Preamble | SFD | Dest MAC | Src MAC  | Type/Len  |    Payload     |   FCS   |
|  7 bytes |  1B |  6 bytes |  6 bytes |  2 bytes  | 46-1500 bytes  | 4 bytes |
+----------+-----+----------+----------+-----------+----------------+---------+

```

1. **Preamble (7 bytes - 56 bits):** Alternating 1s and 0s (`10101010...`) for receiver clock synchronization.
2. **SFD (Start Frame Delimiter, 1 byte):** The sequence `10101011` indicating that the next byte is the Destination MAC address.
3. **Destination MAC (6 bytes - 48 bits):** Target Layer 2 hardware address.
4. **Source MAC (6 bytes - 48 bits):** Originating Layer 2 hardware address.
5. **EtherType / Length (2 bytes):**
* If value is $\ge 1536$ (`0x0600`), it designates the upper-layer **EtherType**:
* `0x0800` $\rightarrow$ IPv4
* `0x86DD` $\rightarrow$ IPv6
* `0x0806` $\rightarrow$ ARP


* *(Note: If an 802.1Q VLAN Tag is present, 4 bytes are inserted immediately before the EtherType field with TPID `0x8100`).*


6. **Payload (Data):** Ranging from **46 to 1500 bytes** (MTU).
* **Padding:** If the encapsulated L3 packet is smaller than 46 bytes, Layer 2 adds padding bytes (all zeros) so the frame meets the minimum **64-byte** requirement.


7. **FCS (Frame Check Sequence, 4 bytes):** **CRC (Cyclic Redundancy Check)** checksum value for error detection.

* **Size Limits:**
* Minimum frame size: **64 bytes** (excluding Preamble/SFD).
* Maximum standard frame size: **1518 bytes** (or **1522 bytes** with an 802.1Q tag).
* Frames $< 64\text{ bytes}$ = **Runts**. Frames $> 1518\text{ bytes}$ = **Giants** (unless Jumbo Frames up to 9216 bytes are enabled).



### MAC Addressing Formats & Multicast Rules

* **Length:** 48 bits (12 hexadecimal characters, EUI-48).
* **OUI (Organizationally Unique Identifier):** The first 24 bits (3 bytes), assigned by the IEEE to the NIC vendor.
* **Device Serial:** The last 24 bits (3 bytes), assigned uniquely by the vendor.
* **Bit Flags:**
* **I/G Bit (Individual/Group):** Least significant bit of the 1st byte. `0` = Unicast, `1` = Multicast/Broadcast.
* **U/L Bit (Universal/Local):** Second least significant bit of the 1st byte. `0` = Globally unique (IEEE OUI), `1` = Locally Administered.


* **Broadcast vs. Multicast MAC Signatures:**
* **Broadcast MAC:** `FF:FF:FF:FF:FF:FF` (all 48 bits set to 1).
* **IPv4 Multicast MAC:** Always begins with **`01:00:5E`** (the remaining 23 bits map the lower bits of the IPv4 multicast address).
* **IPv6 Multicast MAC:** Always begins with **`33:33`** (the lower 32 bits map from the IPv6 multicast address).



### Collision Domains vs Broadcast Domains

* **Collision Domain:** Physical network segment where concurrent frame transmissions cause collisions.
* Every switch port operating in Full-Duplex represents an **isolated collision domain**.
* A Hub (or shared bus segment) forms **a single shared collision domain** across all connected ports.


* **Broadcast Domain:** Logical network segment where a broadcast frame reaches every connected host.
* A Layer 2 switch represents **a single broadcast domain** by default (partitioned only via VLANs).
* A router terminates and partitions broadcast domains on every routed interface.



### Layer 2 Switching Operation (CAM Table)

1. **Learning Phase:** The switch inspects the **Source MAC** address of every incoming frame. If absent from the MAC Address Table (CAM Table), it records the MAC address, ingress port, and VLAN ID, starting an aging timer (default: 300 seconds). If already present, it refreshes the timer.
2. **Forwarding / Filtering Phase:** The switch inspects the **Destination MAC**:
* *Known Unicast:* If Destination MAC exists in the CAM table, forward the frame out that specific interface **only**.
* *Unknown Unicast:* If Destination MAC is absent from the CAM table, **flood** the frame out all ports in the assigned VLAN except the ingress port.
* *Broadcast (`FF:FF:FF:FF:FF:FF`) & Multicast:* **Flood** the frame out all ports in the assigned VLAN except the ingress port.
* *Filtering:* If Destination MAC resides on the same port from which the frame was received, drop/filter the frame.


3. **CAM Table Overflow / MAC Flooding Attack:** An attacker sends thousands of bogus frames with randomized source MACs. The CAM table fills to capacity, preventing the switch from learning new addresses. The switch enters a **fail-open** state (forwarding unicast traffic as unknown unicast by flooding), allowing the attacker to sniff traffic. Mitigation: **Port Security**.

### Address Resolution Protocol (ARP)

* **Purpose:** Resolves a known IPv4 address to an unknown Layer 2 MAC address on the local link.
* **ARP Messages:**
* **ARP Request:** "Who has IP $X$? Tell MAC $Y$". Broadcast frame at Layer 2 (`FF:FF:FF:FF:FF:FF`), Unicast at Layer 3.
* **ARP Reply:** "I have IP $X$, my MAC is $Z$". Unicast frame at Layer 2.


* **Proxy ARP:** A router replies with its own MAC address to an ARP request from a local host looking for a remote IP, provided the router has an active route to that destination (useful when hosts lack a default gateway).
* **Gratuitous ARP (GARP):** A host broadcasts an ARP request for **its own IP address** (Source IP = Target IP).
* *Purposes:* (1) Detect duplicate IP conflicts. (2) Update neighbor ARP caches and switch CAM tables after a hardware failover (e.g., HSRP/VRRP virtual MAC handoff).



### Neighbor Discovery (CDP vs LLDP)

* **CDP (Cisco Discovery Protocol):** Cisco-proprietary, enabled by default, Layer 2 multicast (`01:00:0C:CC:CC:CC`), sends advertisements every 60 seconds (holdtime 180 seconds). Shares: Device ID, local/remote ports, platform, capabilities, native VLAN, VTP domain, duplex.
* **LLDP (Link Layer Discovery Protocol - IEEE 802.1AB):** Open standard, uses Layer 2 multicast (`01:80:C2:00:00:0E`), transmit timer 30 seconds (holdtime 120 seconds). Advertises Chassis ID, Port ID, system name, capabilities, and management IP using TLVs (Type-Length-Value).

---

## 1.5 Layer 4: TCP vs. UDP & Application Port Numbers

### TCP (Transmission Control Protocol - RFC 793)

* **Characteristics:** Connection-oriented, Reliable (ACKs), In-order sequencing, Flow control (dynamic windowing), Congestion avoidance.
* **TCP Header (20–60 bytes):**

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|          Source Port          |       Destination Port        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        Sequence Number                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Acknowledgment Number                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  Data |           |U|A|P|R|S|F|                               |
| Offset| Reserved  |R|C|S|S|Y|I|            Window             |
| (4b)  |   (6b)    |G|K|H|T|N|N|                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           Checksum            |        Urgent Pointer         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Options (0 - 40 bytes)                     |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

```

* **Three-Way Handshake:**
1. `Host A -> Host B`: **SYN** (Seq = $X$)
2. `Host B -> Host A`: **SYN-ACK** (Seq = $Y$, Ack = $X + 1$)
3. `Host A -> Host B`: **ACK** (Seq = $X + 1$, Ack = $Y + 1$)


* **Session Termination (Four-Way Teardown):**
* `A -> B: FIN` $\rightarrow$ `B -> A: ACK`
* `B -> A: FIN` $\rightarrow$ `A -> B: ACK`
* *RST Flag (Reset):* Abrupt connection teardown when a segment arrives at a closed/unbound port or encounters a fatal protocol mismatch.


* **TCP Control Flags (6 bits):**
* **URG:** Urgent Pointer field is valid.
* **ACK:** Acknowledgment field is valid (present in all segments following the initial SYN).
* **PSH:** Forces immediate delivery of buffered data directly up to the application layer.
* **RST:** Aborts and resets the connection immediately.
* **SYN:** Synchronizes Initial Sequence Numbers (ISN) during session establishment.
* **FIN:** Sender has completed transmission; initiates graceful teardown.


* **Flow Control & Windowing:**
* **Window Size:** The volume of bytes a receiver is prepared to accept before requiring an acknowledgment.
* **TCP Zero Window:** Receiver sends `Window Size = 0` when its receive buffer is saturated. Sender halts transmission until receiving an updated window $> 0$.


* **MSS (Maximum Segment Size):** Payload data limit per segment:

$$\text{MSS} = \text{MTU (1500)} - 20\text{ (IP Header)} - 20\text{ (TCP Header)} = \mathbf{1460\text{ bytes}}$$

### UDP (User Datagram Protocol - RFC 768)

* **Characteristics:** Connectionless, Unreliable (no ACKs), No retransmissions, No sequencing, No flow control. Minimal latency overhead.
* **UDP Header (Fixed 8 bytes):**
* `Source Port` (16 bits)
* `Destination Port` (16 bits)
* `Length` (16 bits - minimum value 8 bytes)
* `Checksum` (16 bits - optional in IPv4, **mandatory** in IPv6)



### Application Port Numbers & DHCP Operations

* **Application Ports Reference:**

| Port | Protocol | Layer 4 | Description |
| --- | --- | --- | --- |
| **20** | FTP-Data | TCP | File Transfer Protocol (data transmission) |
| **21** | FTP-Control | TCP | File Transfer Protocol (control/commands) |
| **22** | SSH / SFTP | TCP | Secure Shell / Secure FTP (encrypted terminal and transfer) |
| **23** | Telnet | TCP | Unencrypted terminal emulation (cleartext) |
| **25** | SMTP | TCP | Simple Mail Transfer Protocol (mail relay) |
| **53** | DNS | UDP / TCP | UDP for queries / TCP for zone transfers and large responses |
| **67** | DHCP (Server) | UDP | Dynamic Host Configuration Protocol (Server listener - BootPS) |
| **68** | DHCP (Client) | UDP | Dynamic Host Configuration Protocol (Client listener - BootPC) |
| **69** | TFTP | UDP | Trivial File Transfer Protocol (unauthenticated firmware/config) |
| **80** | HTTP | TCP | Hypertext Transfer Protocol (cleartext web) |
| **88** | Kerberos | TCP/UDP | Network authentication protocol |
| **110** | POP3 | TCP | Post Office Protocol v3 (mailbox download) |
| **123** | NTP | UDP | Network Time Protocol (clock synchronization) |
| **143** | IMAP4 | TCP | Internet Message Access Protocol (advanced mailbox sync) |
| **161** | SNMP | UDP | Simple Network Management Protocol queries (Manager $\rightarrow$ Agent) |
| **162** | SNMP-Trap | UDP | SNMP Traps / Informs (Agent $\rightarrow$ Manager) |
| **389** | LDAP | TCP/UDP | Lightweight Directory Access Protocol |
| **443** | HTTPS | TCP | HTTP over TLS/SSL (secure web) |
| **514** | Syslog | UDP | System logging protocol |
| **636** | LDAPS | TCP | Secure LDAP over TLS/SSL |

* **DHCP DORA Operation:**

| Step | Packet | Source IP | Dest IP | L2 Dest MAC | Details |
| --- | --- | --- | --- | --- | --- |
| **D** | **Discover** | `0.0.0.0` | `255.255.255.255` | `FF:FF:FF:FF:FF:FF` | Client requests lease |
| **O** | **Offer** | Server IP | `255.255.255.255` (or Unicast) | Broadcast/Unicast | Server offers lease parameters |
| **R** | **Request** | `0.0.0.0` | `255.255.255.255` | `FF:FF:FF:FF:FF:FF` | Client formally confirms selection |
| **A** | **ACK** | Server IP | `255.255.255.255` (or Unicast) | Broadcast/Unicast | Server commits the binding |

* **Key DHCP Options:**
* **Option 3:** Default Gateway router IP.
* **Option 6:** Domain Name System (DNS) server IP.
* **Option 43:** WLC IPv4 address (Vendor-specific information used by APs).
* **Option 150:** Cisco proprietary TFTP server list for IP Phones.


* **DHCP Relay Agent (`ip helper-address`):**
* Applied on incoming routed switch/router interface receiving client broadcasts.
* Forwards client broadcasts as **Unicast** packets to remote servers across routed boundaries.
* Forwards 8 UDP ports by default: Time (37), TACACS (49), DNS (53), BOOTP/DHCP Server (67), BOOTP/DHCP Client (68), TFTP (69), NetBIOS Name Service (137), NetBIOS Datagram (138).



---

## 1.6 IPv4 Addressing, Subnetting & Packet Structure

### IPv4 Header Breakdown (20 to 60 bytes)

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|Version|  IHL  |Type of Service|          Total Length         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|         Identification        |Flags|      Fragment Offset    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  Time to Live |    Protocol   |        Header Checksum        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                       Source Address                          |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Destination Address                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Options                    |    Padding    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

```

* **Version (4 bits):** Always `4` (`0100`).
* **IHL (Internet Header Length, 4 bits):** Header length in 32-bit words. Minimum value: `5` ($5 \times 4 = 20\text{ bytes}$).
* **Type of Service / DSCP (8 bits):** QoS (Differentiated Services Code Point - 6 bits) & ECN (Explicit Congestion Notification - 2 bits).
* **Total Length (16 bits):** Entire packet size in bytes (Header + Payload). Maximum: 65,535 bytes.
* **Fragmentation Fields (32 bits total):**
* *Identification (16 bits):* Unique integer binding all fragments of an originating packet together.
* *Flags (3 bits):*
* Bit 0: Reserved (always 0).
* Bit 1: **DF (Don't Fragment):** If set to `1`, routers cannot fragment the packet. If the packet exceeds the next link MTU, it is dropped and an ICMP Type 3 Code 4 error is returned.
* Bit 2: **MF (More Fragments):** `1` = additional fragments follow, `0` = this is the final fragment.


* *Fragment Offset (13 bits):* Position of the fragment payload relative to the original payload, measured in **8-byte blocks**:



$$\text{Fragment Offset} = \frac{\text{Accumulated Previous Payload Bytes}}{8}$$

* **Path MTU Discovery (PMTUD):** Host sets DF = 1 on outgoing packets. If an intermediate router's MTU is exceeded, the router drops the packet and sends an **ICMP Type 3 Code 4** ("Fragmentation Needed and DF Set") informing the sender of the maximum permissible MTU on that bottleneck link.
* **TTL (Time to Live, 8 bits):** Decremented by 1 at each router hop. Prevents routing loops. If $\text{TTL} = 0$, packet is dropped and `ICMP Type 11 Code 0 (Time Exceeded)` is returned.
* **Protocol (8 bits):** Layer 4 encapsulated protocol:
* `1` = ICMP
* `6` = TCP
* `17` = UDP
* `47` = GRE
* `89` = OSPF


* **Header Checksum (16 bits):** Error detection for the IPv4 header only (recomputed at every hop due to TTL changes).
* **Source & Destination IP Addresses:** 32 bits each.

### ICMP Messages Reference (Internet Control Message Protocol)

* **Type 8 / Code 0:** Echo Request (outbound ping).
* **Type 0 / Code 0:** Echo Reply (ping reply).
* **Type 11 / Code 0:** Time-to-Live Exceeded in Transit (Traceroute hop response).
* **Type 3: Destination Unreachable:**
* *Code 0:* Net Unreachable (no route in routing table).
* *Code 1:* Host Unreachable (local subnet reached, but target host fails ARP).
* *Code 2:* Protocol Unreachable.
* *Code 3:* Port Unreachable (host reached, but listening UDP port is closed).
* *Code 4:* Fragmentation Needed and DF Set (exceeds link MTU).



### Subnetting Reference Table

| Prefix | Subnet Mask | Wildcard Mask | Total IPs | Usable Hosts | Block Size (Magic Number) |
| --- | --- | --- | --- | --- | --- |
| **/24** | 255.255.255.0 | 0.0.0.255 | 256 | 254 | 256 |
| **/25** | 255.255.255.128 | 0.0.0.127 | 128 | 126 | 128 |
| **/26** | 255.255.255.192 | 0.0.0.63 | 64 | 62 | 64 |
| **/27** | 255.255.255.224 | 0.0.0.31 | 32 | 30 | 32 |
| **/28** | 255.255.255.240 | 0.0.0.15 | 16 | 14 | 16 |
| **/29** | 255.255.255.248 | 0.0.0.7 | 8 | 6 | 8 |
| **/30** | 255.255.255.252 | 0.0.0.3 | 4 | 2 | 4 |
| **/31** | 255.255.255.254 | 0.0.0.1 | 2 | 2 (RFC 3021 P2P) | 2 |
| **/32** | 255.255.255.255 | 0.0.0.0 | 1 | 1 (Host/Loopback) | 1 |

* **Calculation Rules:**
* $\text{Total IPs} = 2^H$, where $H = 32 - \text{prefix}$.
* $\text{Usable Hosts} = 2^H - 2$ (subtract Network ID and Broadcast).
* $\text{Magic Number (Block Size)} = 256 - \text{value of interesting octet in mask}$.


* *Valid Subnet Mask Values:* `0`, `128`, `192`, `224`, `240`, `248`, `252`, `254`, `255`.

### Address Classes & Special Ranges

* **Classful Ranges:**
* **Class A:** `1.0.0.0` to `126.255.255.255` (Default: `/8` - `255.0.0.0`).
* **Class B:** `128.0.0.0` to `191.255.255.255` (Default: `/16` - `255.255.0.0`).
* **Class C:** `192.0.0.0` to `223.255.255.255` (Default: `/24` - `255.255.255.0`).
* **Class D:** `224.0.0.0` to `239.255.255.255` (Multicast).
* **Class E:** `240.0.0.0` to `255.255.255.255` (Experimental / Reserved).


* **Private Address Space (RFC 1918):**
* `10.0.0.0/8` (`10.0.0.0` – `10.255.255.255`)
* `172.16.0.0/12` (`172.16.0.0` – `172.31.255.255`)
* `192.168.0.0/16` (`192.168.0.0` – `192.168.255.255`)


* **Special IPv4 Ranges:**
* **Loopback:** `127.0.0.0/8` (`127.0.0.1` to `127.255.255.254`). Local stack diagnostics.
* **APIPA (Automatic Private IP Addressing - RFC 3927):** `169.254.0.0/16` (`169.254.0.1` to `169.254.255.254`). Self-assigned when DHCP fails; strictly non-routable.
* **Carrier-Grade NAT (RFC 6598):** `100.64.0.0/10` (`100.64.0.0` to `100.127.255.255`).



### Host Routing & Default Gateway Logic

* **Bitwise AND Operation:** A host performs a logical AND operation between the Destination IP and its configured subnet mask:
* If $\text{Result} = \text{Local Network ID}$: Destination is **Local**. Host checks local ARP cache or sends an ARP Request directly for destination MAC.
* If $\text{Result} \ne \text{Local Network ID}$: Destination is **Remote**. Host encapsulates frame with the MAC address of its **Default Gateway** (resolving via ARP if not cached).


* **Default Gateway Rule:** The Default Gateway IP address **must** reside within the exact same local IP subnet as the client host interface.

---

## 1.7 IPv6 Architecture, Addressing & Discovery

### Address Structure & Compression Rules

* Length: **128 bits** (16 bytes), formatted as 8 groups (hextets) of 4 hexadecimal digits (16 bits per hextet) separated by colons (`:`).
* **Rule 1 (Omit Leading Zeros):** Leading zeros within any 16-bit hextet can be dropped (`0042` $\rightarrow$ `42`, `0db8` $\rightarrow$ `db8`, `0000` $\rightarrow$ `0`).
* **Rule 2 (Double Colon `::`):** A single contiguous sequence of all-zero hextets can be compressed to `::`. Permitted **strictly once** per address.
* *Example:* `2001:0db8:0000:0000:0000:0000:0042:0001` $\rightarrow$ `2001:db8::42:1`.



### IPv6 Fixed Header (40 Bytes)

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|Version| Traffic Class |           Flow Label                  |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|         Payload Length        |  Next Header  |   Hop Limit   |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+                                                               +
|                                                               |
+                         Source Address                        +
|                           (128 bits)                          |
+                                                               +
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+                                                               +
|                                                               |
+                      Destination Address                      +
|                           (128 bits)                          |
+                                                               +
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

```

* **Version (4 bits):** Always `6` (`0110`).
* **Traffic Class (8 bits):** Replaces IPv4 ToS/DSCP for QoS markings.
* **Flow Label (20 bits):** Marks packets within the same traffic flow for uniform handling by routers.
* **Payload Length (16 bits):** Size of the payload in bytes (includes extension headers; excludes the fixed 40-byte base header).
* **Next Header (8 bits):** Replaces IPv4 Protocol field. Identifies the type of extension header following the IPv6 header, or the upper-layer protocol (TCP = 6, UDP = 17, ICMPv6 = 58).
* **Hop Limit (8 bits):** Replaces IPv4 TTL. Decremented by 1 at each router hop.
* **Source & Destination Addresses:** 128 bits each.
* *Note:* IPv6 **eliminates the Header Checksum** to accelerate router processing. Intermediate routers never fragment packets; fragmentation is performed solely by the sending host using Path MTU Discovery (PMTUD).

### IPv6 Address Types, Scopes & GUA Structure

* **Global Unicast Address (GUA):** Globally routable on the public Internet. Prefix range: **`2000::/3`** (allocations start with `2xxx:` or `3xxx:`).
* **Structure of a `/64` GUA:**
* *Global Routing Prefix (Bits 1–48):* Assigned by ISP or RIR.
* *Subnet ID (Bits 49–64):* 16 bits providing $2^{16} = 65,536$ individual subnets per site.
* *Interface ID (Bits 65–128):* 64-bit host identifier.




* **Unique Local Address (ULA - RFC 4193):** Private addresses for internal organizational routing (analogous to RFC 1918). Prefix: **`fc00::/7`** (in practice, bit 8 is always 1, so all ULAs begin with **`fd00::/8`**).
* **Link-Local Address (LLA):** Mandatory on every active IPv6 interface. Scoped **strictly to the local physical link/VLAN**; intermediate routers never forward them.
* Prefix: **`fe80::/10`** (in practice `fe80::` through `febf::`).
* Used as next-hop for routing protocols (OSPFv3, EIGRPv6) and default gateway for hosts.


* **Multicast:** Starts with **`ff00::/8`**. Completely replaces broadcast functionality.
* **Multicast Scopes (4th nibble `ff0X::`):**
* `ff01::` = Interface-Local Scope (never leaves device).
* `ff02::` = Link-Local Scope (never forwarded by routers).
* `ff05::` = Site-Local Scope.
* `ff0e::` = Global Scope.


* *Well-Known Link-Local Groups:*
* `ff02::1` = All Nodes
* `ff02::2` = All Routers
* `ff02::5` = OSPFv3 All Routers
* `ff02::6` = OSPFv3 Designated Routers (DR)


* *Solicited-Node Multicast Address:* Used by NDP for Layer 2 address resolution. Constructed by appending `ff02::1:ff00:0/104` to the **last 24 bits** of the target interface's unicast address (`ff02::1:ffXX:XXXX`).


* **Anycast:** One-to-nearest delivery model. Same address assigned to multiple servers; routing delivers packets to topologically closest node.
* **Loopback:** **`::1/128`** (local stack diagnostic address).
* **Unspecified Address:** **`::/128`** (all zeros). Used strictly as a source IP placeholder during initial boot and Duplicate Address Detection (DAD).
* **Documentation Prefix (RFC 3849):** **`2001:db8::/32`** (reserved for documentation and lab examples).

### EUI-64 (Extended Unique Identifier)

Converts a 48-bit MAC address into a 64-bit Interface ID:

1. Split the 48-bit MAC into two equal 24-bit halves.
2. Insert **`FF:FE`** (16 bits) in the middle.
3. Invert the **7th bit** (Universal/Local bit) of the first byte (0 becomes 1, 1 becomes 0).

* **Exam Shortcut:** Modify the **2nd hexadecimal digit** of the MAC by adding or subtracting 2:
* `0` $\leftrightarrow$ `2` | `4` $\leftrightarrow$ `6` | `8` $\leftrightarrow$ `A` | `C` $\leftrightarrow$ `E`
* *Example:* MAC `00:50:56:A1:B2:C3` $\rightarrow$ `00` becomes `02`. Result: `0250:56FF:FEA1:B2C3`.



### NDP (Neighbor Discovery Protocol - RFC 4861) & SLAAC

NDP operates via ICMPv6 to replace ARP, ICMP Router Discovery, and DHCP addressing:

* **Router Solicitation (RS - Type 133):** Host queries local routers for network configuration (multicast to `ff02::2`).
* **Router Advertisement (RA - Type 134):** Routers periodically announce prefixes, prefix lengths, default gateway parameters, MTU, and autoconfiguration flags (multicast to `ff02::1`).
* **Neighbor Solicitation (NS - Type 135):** Resolves a neighbor Layer 2 MAC address (sent to target's Solicited-Node Multicast) and executes DAD.
* **Neighbor Advertisement (NA - Type 136):** Unicast response containing the Layer 2 MAC address (replaces ARP Reply).
* **DAD (Duplicate Address Detection):** Before binding an IPv6 address, a host sends an NS querying its own proposed address with `Source IP = ::`. If no NA reply is received, the address is confirmed unique.
* **SLAAC (Stateless Address Autoconfiguration):**
* Host learns its `/64` prefix from the Router Advertisement.
* Host autonomously generates its 64-bit Interface ID (via EUI-64 or Random Privacy Extensions).
* **RA Configuration Flags:**
* **A-flag (Autonomous):** `1` = Generate IP address autonomously using SLAAC.
* **M-flag (Managed):** `1` = Use **Stateful DHCPv6** (DHCPv6 server provides both IPv6 address and DNS).
* **O-flag (Other):** `1` = Use **Stateless DHCPv6** (SLAAC generates IP; DHCPv6 server provides DNS/domain name).





---

## 1.8 Client OS Network Verification & Troubleshooting

### Windows Commands

* `ipconfig`: Displays IPv4 address, Subnet Mask, and Default Gateway.
* `ipconfig /all`: Displays physical MAC address, DHCP server IP, DNS servers, lease timestamps, and IPv6 DAD state.
* `ipconfig /release` & `ipconfig /renew`: Releases and re-negotiates DHCP lease bindings.
* `ipconfig /flushdns`: Clears the local DNS resolver cache.
* `route print` (or `netstat -r`): Displays local IPv4/IPv6 routing tables and the default route (`0.0.0.0 0.0.0.0`).
* `arp -a`: Displays current ARP cache table (IP-to-MAC resolutions).
* `netstat -ano`: Displays active TCP/UDP socket connections, listening ports, and corresponding PIDs.
* *APIPA Identification:* An address in `169.254.X.X` with an empty Default Gateway indicates DHCP server exhaustion or complete DHCP communication failure.

### Linux Commands (iproute2 vs Legacy)

* `ip addr` (or `ip a`): Displays network interfaces, MAC addresses (`link/ether`), and IPv4/IPv6 addresses with CIDR prefixes.
* `ip route` (or `ip r`): Displays routing table and default gateway (`default via <gateway-ip> dev <interface>`).
* `ip neigh`: Displays neighbor table (ARP cache).
* `cat /etc/resolv.conf`: Displays active DNS resolver nameservers (`nameserver <IP>`).
* `ss -tulpn`: Displays active listening sockets and owning processes (modern replacement for `netstat -tulpn`).

### macOS Commands

* `ifconfig <interface>`: Displays IP address, MAC address, and subnet mask (often shown in hex: `0xffffff00` = `255.255.255.0`).
* `networksetup -getinfo Wi-Fi`: Summarizes IP, subnet mask, default gateway router, and physical MAC address.
* `networksetup -getdnsservers Wi-Fi`: Displays configured DNS servers.
* `route -n get default`: Displays the current active default gateway route.

### Troubleshooting Scenarios (Root Cause Logic)

1. **Incorrect Default Gateway:** Host successfully communicates with local endpoints on the same subnet/VLAN, but cannot route traffic to external subnets or the Internet.
2. **Incorrect or Missing DNS:** Host pings numerical external IP addresses successfully (e.g., `ping 8.8.8.8`), but fails to resolve domain names (e.g., `ping google.com`) and web pages fail to resolve.
3. **Incorrect Subnet Mask:** Host misinterprets remote destination IPs as local (sending direct ARP requests that fail), or local destination IPs as remote (improperly forwarding local packets to the gateway).
4. **Duplicate IP Address:** Causes intermittent connection drops, CAM table flapping on access switches, and operating system IP conflict alerts.

---

## 1.9 Master Cisco IOS CLI Reference (Domain 1 Scope)

```cisco
! ==============================================================================
! 1. INITIAL SYSTEM BASELINE CONFIGURATION
! ==============================================================================
configure terminal
 hostname SW1
 enable secret cisco123               ! Strongly hashed Privileged EXEC secret
 service password-encryption          ! Reversibly encrypt cleartext passwords
 no ip domain-lookup                  ! Prevent hung CLI on typo broadcasts
 banner motd # AUTHORIZED ACCESS ONLY #
 line console 0
  password ciscoconsole
  login
  logging synchronous                 ! Prevent syslog messages interrupting input
  exec-timeout 5 0                    ! 5-minute inactivity auto-logout
 exit
 line vty 0 15
  transport input ssh                 ! Force encrypted SSH only; disable Telnet
  password ciscovty
  login
 exit

! ==============================================================================
! 2. INTERFACE STATUS & HARDWARE DIAGNOSTICS
! ==============================================================================
show ip interface brief              ! Display L1 status, L2 protocol state, and assigned IPv4 addresses
show ipv6 interface brief            ! Display L1/L2 states, Link-Local (fe80::), and GUA per interface
show interfaces status               ! Switchport operational status, VLAN assignment, duplex, and speed
show interfaces <id>                 ! Detailed counters: MTU, bandwidth, CRC errors, runts, giants, late collisions
show controllers <id>                ! Inspect hardware microcode, DCE/DTE clock rate, and physical chipsets
show power inline                    ! Display PoE status, wattage consumption, and power class per port

! ==============================================================================
! 3. SWITCHING, FORWARDING & DISCOVERY PROTOCOLS
! ==============================================================================
show mac address-table dynamic       ! Display dynamically learned MAC addresses per port and VLAN
clear mac address-table dynamic      ! Flush dynamic entries from the CAM table
show cdp neighbors                   ! Summary of directly connected Cisco devices and local/remote ports
show cdp neighbors detail            ! Detailed CDP view: neighbor IPv4/IPv6 addresses and IOS version
show cdp entry <device-id>           ! Display detailed CDP information for a specific neighbor device
show lldp neighbors                  ! Summary of directly connected LLDP neighbors (IEEE 802.1AB)
show lldp neighbors detail           ! Detailed LLDP view (TLVs, system capabilities, management IPs)

! ==============================================================================
! 4. ROUTING, NEIGHBOR TABLES & PROTOCOL MAPPING
! ==============================================================================
show ip route                        ! Display IPv4 routing table and Gateway of Last Resort
show ipv6 route                      ! Display IPv6 routing table and next-hop LLAs
show ip arp                          ! Display IPv4 ARP cache table (IP-to-MAC bindings)
clear ip arp-cache                   ! Flush ARP cache entries from router memory
show ipv6 neighbors                  ! Display IPv6 NDP neighbor table (replaces show ip arp)
show ipv6 interface <id>             ! Inspect IPv6 RAs, joined multicast groups, and M/O SLAAC flags

! ==============================================================================
! 5. SYSTEM MANAGEMENT, MEMORY & ENVIRONMENT
! ==============================================================================
show version                         ! Device uptime, active IOS image path in Flash, RAM/NVRAM sizes, and Config Register (e.g., 0x2102)
show running-config                  ! Display active configuration currently running in volatile RAM
show startup-config                  ! Display saved configuration stored in non-volatile NVRAM
copy running-config startup-config   ! Save active configuration changes from RAM to NVRAM (alias: write)
show flash:                          ! List files stored in Flash memory (IOS .bin images)
show hosts                           ! Display local DNS cache and static hostname-to-IP mappings
terminal length 0                    ! Disable the --More-- screen pause for continuous CLI output

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