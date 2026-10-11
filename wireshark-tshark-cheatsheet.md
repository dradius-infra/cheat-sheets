# Wireshark & TShark Packet Analysis Cheat Sheet

## 1. TShark CLI Fundamentals, Capture Operations & Automation

| Task | Command | Description | 
| ----- | ----- | ----- | 
| **List Network Interfaces** | `tshark -D` | Displays all physical and virtual interfaces with assigned index numbers | 
| **Capture on Specific Interface** | `tshark -i en0` | Starts live capture on interface `en0` (or index number) | 
| **Capture with Packet Limit** | `tshark -i en0 -c 100` | Captures exactly 100 packets and exits | 
| **Capture with Duration Limit** | `tshark -i en0 -a duration:60` | Automatically stops capture after 60 seconds | 
| **Write Packets to PCAP File** | `tshark -i en0 -w capture.pcap` | Saves raw captured packets to disk without terminal stdout parsing | 
| **Read from PCAP File** | `tshark -r capture.pcap` | Reads and dissects an existing packet capture file | 
| **Apply Display Filter to File** | `tshark -r capture.pcap -Y "dns"` | Reads capture file through the display filter engine | 
| **Disable DNS Resolution (Fast)** | `tshark -n -r capture.pcap` | Disables L3/L4 name resolution for faster CLI processing | 
| **Extract Specific Fields (CSV)** | `tshark -r capture.pcap -T fields -e ip.src -e ip.dst -e tcp.dstport -E header=y -E separator=,` | Exports discrete protocol headers into structured CSV format with column headers | 
| **Live Stream DNS Domain Queries** | `tshark -i en0 -f "udp port 53" -T fields -e ip.src -e dns.qry.name` | Real-time terminal watcher showing who requests which domain name | 
| **Export to JSON** | `tshark -r capture.pcap -T json > capture.json` | Dumps dissected packet tree into standard JSON format | 
| **Full Verbose Dissection** | `tshark -r capture.pcap -V -c 1` | Dumps complete multi-layer protocol breakdown (packet details) for 1 packet | 
| **Show Hex and ASCII Dump** | `tshark -r capture.pcap -x -c 1` | Dumps packet bytes in hexadecimal and ASCII side-by-side | 
| **Quiet Terminal Mode** | `tshark -q -r capture.pcap -z io,stat,1` | Suppresses per-packet printing and outputs aggregated I/O statistics | 
| **Protocol Hierarchy Statistics** | `tshark -q -r capture.pcap -z io,phs` | Generates a protocol breakdown tree showing distribution of traffic types | 
| **Top IP Conversations** | `tshark -q -r capture.pcap -z conv,ip` | Summarizes all Layer 3 communications with bytes and frame counts | 
| **Top TCP Conversations** | `tshark -q -r capture.pcap -z conv,tcp` | Displays all TCP socket pairs, port numbers, duration, and volume | 
| **Follow TCP Stream via CLI** | `tshark -r capture.pcap -q -z follow,tcp,ascii,0` | Reassembles and dumps stream 0 payload in readable ASCII format | 
| **Ring Buffer Rolling Capture** | `tshark -i en0 -b filesize:50000 -b files:5 -w ring.pcap` | Keeps 5 rotating files of 50MB each to prevent disk exhaustion | 

## 2. Capture Filters (BPF Syntax - `-f "..."`)

*Note: Capture filters use Berkeley Packet Filter (BPF) syntax evaluated by libpcap at kernel/driver level prior to packet buffering.*

| Task | Filter Expression | Description | 
| ----- | ----- | ----- | 
| **Filter Specific IPv4 Host** | `host 192.168.1.50` | Captures traffic where source or destination IP matches | 
| **Filter Specific Subnet** | `net 192.168.1.0/24` | Captures traffic within the specified CIDR block | 
| **Filter Source IP Only** | `src host 10.0.0.1` | Captures packets originating strictly from source IP | 
| **Filter Destination IP Only** | `dst host 10.0.0.1` | Captures packets addressed strictly to target IP | 
| **Filter Specific Port (TCP/UDP)** | `port 53` | Captures DNS traffic on TCP or UDP port 53 | 
| **Filter UDP Port Only (e.g., DNS)** | `udp port 53` | Isolates standard UDP DNS traffic | 
| **Filter Port Range** | `portrange 20-25` | Captures traffic on ports 20 through 25 | 
| **Filter Source Port Only** | `src port 443` | Captures server response traffic from HTTPS port | 
| **Capture Only ICMP (Ping)** | `icmp` | Captures IPv4 ICMP ping and error messages | 
| **Capture Only ARP** | `arp` | Captures Layer 2 Address Resolution Protocol frames | 
| **Capture Only IPv6** | `ip6` | Isolates all native IPv6 packet frames | 
| **Exclude Specific Host** | `not host 192.168.1.1` | Drops traffic originating from or destined to specified gateway | 
| **Filter Broadcast Frames** | `ether broadcast` | Captures Layer 2 broadcast packets (`ff:ff:ff:ff:ff:ff`) | 
| **Filter Multicast Frames** | `ether multicast` | Captures Layer 2 multicast packets | 
| **Compound AND/OR Logic** | `host 192.168.1.50 and (port 80 or port 443)` | Captures web traffic strictly for host 192.168.1.50 | 
| **Filter by Packet Length** | `greater 1000` | Captures only packets larger than 1000 bytes | 

## 3. L2 / L3 Display Filters (Ethernet, ARP, VLAN, IPv4, IPv6)

| Task | Display Filter | Description | 
| ----- | ----- | ----- | 
| **Filter by MAC Address** | `eth.addr == 00:11:22:33:44:55` | Isolates frames matching source or destination MAC | 
| **Filter by Source MAC** | `eth.src == 00:11:22:33:44:55` | Isolates frames originating from specific physical hardware | 
| **Filter by Vendor OUI** | `eth.addr[0:3] == 00:0c:42` | Matches vendor-specific MAC prefix (e.g., MikroTik `00:0C:42`) | 
| **Detect Ethernet Broadcast** | `eth.dst == ff:ff:ff:ff:ff:ff` | Filters all Layer 2 broadcast discovery frames | 
| **Filter 802.1Q VLAN Tag** | `vlan.id == 10` | Isolates traffic tagged with VLAN ID 10 | 
| **Filter ARP Requests** | `arp.opcode == 1` | Isolates "Who has IP X? Tell IP Y" queries | 
| **Filter ARP Replies** | `arp.opcode == 2` | Isolates "IP X is at MAC Y" resolution frames | 
| **Detect Duplicate IP / Conflict** | `arp.duplicate-address-detected` | Flags gratuitous ARP packets indicating IP address collision | 
| **Filter Single IPv4 Address** | `ip.addr == 192.168.1.1` | Matches packets where source or destination is target IP | 
| **Filter Traffic Between Two IPs** | `ip.addr == 192.168.1.10 and ip.addr == 192.168.1.1` | Isolates direct conversation between two endpoints | 
| **Filter IPv4 Subnet Range** | `ip.addr == 192.168.1.0/24` | Evaluates all traffic inside a local CIDR boundary | 
| **Filter Low TTL (Traceroute/Loops)** | `ip.ttl <= 2` | Highlights packets nearing hop expiration | 
| **Filter Fragmented IPv4 Packets** | `ip.flags.mf == 1 or ip.frag_offset > 0` | Shows packets split into multiple fragments | 
| **Filter by DSCP / QoS Marking** | `ip.dsfield.dscp != 0` | Shows packets with explicit Quality of Service priority tags | 
| **Filter IPv6 Addresses** | `ipv6.addr == 2001:db8::1` | Matches specified IPv6 unicast or multicast address | 

## 4. L4 Transport Layer Display Filters (TCP & UDP)

| Task | Display Filter | Description | 
| ----- | ----- | ----- | 
| **Filter by TCP Port** | `tcp.port == 443` | Matches HTTPS traffic regardless of source or destination | 
| **Filter by UDP Port** | `udp.port == 53` | Matches UDP DNS queries and responses | 
| **Filter Destination Port Only** | `tcp.dstport == 22` | Isolates inbound/outbound connection requests to SSH | 
| **TCP 3-Way Handshake SYN** | `tcp.flags.syn == 1 and tcp.flags.ack == 0` | Isolates outbound initial connection requests | 
| **TCP Handshake SYN-ACK** | `tcp.flags.syn == 1 and tcp.flags.ack == 1` | Isolates server handshake acceptance packets | 
| **TCP Connection Teardown (FIN)** | `tcp.flags.fin == 1` | Flags clean connection termination requests | 
| **TCP Abrupt Reset (RST)** | `tcp.flags.reset == 1` | Highlights refused connections or hard socket terminations | 
| **Filter Specific TCP Stream ID** | `tcp.stream eq 4` | Isolates conversation stream index 4 | 
| **Detect Out-of-Order Packets** | `tcp.analysis.out_of_order` | Flags packet delivery sequence inversions | 
| **Zero Window Warnings** | `tcp.analysis.zero_window` | Flags receiver buffer exhaustion events | 
| **Window Full Warnings** | `tcp.analysis.window_full` | Sender has filled receiver advertised receive buffer | 
| **TCP Keep-Alive Packets** | `tcp.analysis.keep_alive` | Identifies probes keeping idle sessions active | 

## 5. Network Diagnostics, Latency & Packet Loss

| Task | Display Filter | Description | 
| ----- | ----- | ----- | 
| **TCP Retransmissions** | `tcp.analysis.retransmission` | Identifies packet loss recovered by standard timers | 
| **TCP Fast Retransmissions** | `tcp.analysis.fast_retransmission` | Identifies packet loss recovered by 3 duplicate ACKs | 
| **Duplicate ACKs** | `tcp.analysis.duplicate_ack` | Shows receiver signalling missing segments to sender | 
| **Lost TCP Segments** | `tcp.analysis.lost_segment` | Identifies packet gaps detected in sequence number tracking | 
| **High Round-Trip Time (RTT)** | `tcp.analysis.ack_rtt > 0.1` | Flags ACK segments taking longer than 100ms | 
| **SYN / Service Latency** | `tcp.analysis.initial_rtt > 0.05` | Handshake latency exceeding 50ms (network or server load) | 
| **Inter-Packet Delay (>1s)** | `frame.time_delta > 1` | Highlights pauses and stalls in session transmission | 
| **ICMP Destination Unreachable** | `icmp.type == 3` | Router/host reporting port, network, or host unreachable | 
| **ICMP Fragmentation Needed (MTU)** | `icmp.type == 3 and icmp.code == 4` | Essential for Path MTU Discovery (PMTUD) black-hole troubleshooting | 
| **ICMP Time-to-Live Exceeded** | `icmp.type == 11` | Packet died in transit (traceroute response or routing loop) | 
| **ICMP Ping Request** | `icmp.type == 8` | Standard ICMP echo query | 
| **ICMP Ping Reply** | `icmp.type == 0` | Standard ICMP echo answer | 
| **Wireshark Expert Warnings** | `_ws.expert.severity == "Warning"` | Filters all protocol violations and performance warnings | 
| **Wireshark Expert Errors** | `_ws.expert.severity == "Error"` | Filters malformed packets and unrecoverable protocol faults | 

## 6. L7 Application Layer (DNS, DHCP, HTTP, TLS/HTTPS)

| Task | Display Filter | Description | 
| ----- | ----- | ----- | 
| **All DNS Queries** | `dns.flags.response == 0` | Isolates outgoing name resolution inquiries | 
| **All DNS Responses** | `dns.flags.response == 1` | Isolates incoming name resolution replies | 
| **DNS Resolution Errors (Any)** | `dns.flags.rcode != 0` | Flags any failed DNS resolution | 
| **DNS Name Error (NXDOMAIN)** | `dns.flags.rcode == 3` | Shows queries for non-existent domain names | 
| **DNS Server Failure (SERVFAIL)** | `dns.flags.rcode == 2` | Upstream DNS server encountered an internal failure | 
| **Filter Specific Queried Domain** | `dns.qry.name contains "wikipedia"` | Filters resolutions matching domain substring | 
| **Filter by DNS Record Type (A)** | `dns.qry.type == 1` | Filters standard IPv4 address lookups | 
| **Filter by DNS Record Type (AAAA)** | `dns.qry.type == 28` | Filters IPv6 address lookups | 
| **Slow DNS Resolutions** | `dns.time > 0.05` | Shows DNS responses taking longer than 50ms | 
| **DHCP Discover** | `dhcp.option.dhcp == 1` | Broadcast request from client seeking an IP lease | 
| **DHCP Offer** | `dhcp.option.dhcp == 2` | Server proposal offering an IP lease | 
| **DHCP Request** | `dhcp.option.dhcp == 3` | Client formal acceptance of an offered IP | 
| **DHCP ACK** | `dhcp.option.dhcp == 5` | Server final confirmation of the lease assignment | 
| **DHCP NAK** | `dhcp.option.dhcp == 6` | Server rejection of client request (e.g. invalid subnet) | 
| **HTTP GET Requests** | `http.request.method == "GET"` | Lists plain HTTP GET operations | 
| **HTTP POST Requests** | `http.request.method == "POST"` | Lists payload submission requests | 
| **HTTP 4xx Client Errors** | `http.response.code >= 400 and http.response.code <= 499` | Isolates unauthorized, forbidden, or missing pages | 
| **HTTP 5xx Server Errors** | `http.response.code >= 500 and http.response.code <= 599` | Isolates upstream gateway and server internal faults | 
| **TLS Client Hello (SNI Inspection)** | `tls.handshake.type == 1` | Shows initial HTTPS connections revealing domain target | 
| **Filter by Target Domain (SNI)** | `tls.handshake.extensions_server_name contains "google"` | Tracks secure connections by target domain name | 
| **TLS Handshake Failures / Alerts** | `tls.record.content_type == 21` | Highlights certificate errors and handshake rejections | 
| **Filter TLS Certificate Exchange** | `tls.handshake.type == 11` | Isolates public certificate delivery from server | 

## 7. Routing Protocols & Network Infrastructure (CCNA Focus)

| Task | Display Filter | Description | 
| ----- | ----- | ----- | 
| **OSPF All Packets** | `ospf` | Filters Open Shortest Path First protocol traffic | 
| **OSPF Hello Packets** | `ospf.msg == 1` | Isolates neighbor discovery and keepalive multicasts | 
| **OSPF Database Description (DBD)** | `ospf.msg == 2` | Shows link-state database synchronization packets | 
| **OSPF Link State Update (LSU)** | `ospf.msg == 4` | Shows route announcements and topology changes | 
| **BGP All Messages** | `bgp` | Filters Border Gateway Protocol control traffic | 
| **BGP OPEN Messages** | `bgp.type == 1` | Isolates peer session establishment | 
| **BGP UPDATE Messages** | `bgp.type == 2` | Shows advertised routes and route withdrawals | 
| **BGP NOTIFICATION (Errors)** | `bgp.type == 3` | Indicates session drops, peer errors, and hold-time timeouts | 
| **CDP Discovery** | `cdp` | Cisco Discovery Protocol frames on adjacent links | 
| **LLDP Discovery** | `lldp` | Link Layer Discovery Protocol (vendor-neutral) | 
| **VRRP Redundancy** | `vrrp` | Virtual Router Redundancy Protocol advertisements | 
| **STP Spanning Tree BPDUs** | `stp` | Spanning tree configuration and topology change notifications | 

## 8. Security, Reconnaissance & Anomaly Detection

| Task | Display Filter | Description | 
| ----- | ----- | ----- | 
| **SYN Port Scan Signature** | `tcp.flags.syn == 1 and tcp.flags.ack == 0 and tcp.window_size <= 1024` | Abnormal tiny-window SYN packets common in Nmap/masscan | 
| **NULL Scan Detection** | `tcp.flags == 0` | Packets with zero flags set (reconnaissance probe) | 
| **XMAS Scan Detection** | `tcp.flags.fin == 1 and tcp.flags.push == 1 and tcp.flags.urg == 1` | FIN, PUSH, and URG flags simultaneously set | 
| **Cleartext Password Search** | `frame contains "password" or frame contains "USER"` | Scans unencrypted payloads for authentication credentials | 
| **Suspicious Oversized ICMP** | `icmp and frame.len > 1000` | Flags ICMP ping tunneling or flood payloads | 
| **DNS Zone Transfer Attempt** | `dns.qry.type == 252` | Identifies AXFR full zone transfer queries | 
| **DNS ANY Query (Amplification)** | `dns.qry.type == 255` | Flags ANY requests often leveraged in reflection DDoS | 

## 9. MikroTik Streaming, TZSP & Remote Troubleshooting

| Task | Filter / Command | Description | 
| ----- | ----- | ----- | 
| **Filter MikroTik Sniffer Stream** | `tzsp` | Isolates TaZmen Sniffer Protocol UDP packets from RouterOS | 
| **Inner Packet Extraction** | `tzsp and ip.addr == 10.0.0.1` | Evaluates encapsulated packets inside TZSP tunnels | 
| **Start RouterOS Sniffer (CLI)** | `/tool/sniffer/set streaming-enabled=yes streaming-server=192.168.1.100` | Redirects router packets to analyst PC via UDP port 37008 | 
| **Stream Live MikroTik into TShark** | `tshark -i en0 -f "udp port 37008" -d udp.port==37008,tzsp` | Decodes RouterOS remote mirror stream directly in terminal | 
| **Stop RouterOS Sniffer (CLI)** | `/tool/sniffer/stop` | Terminates router-side mirror streaming to save CPU | 

## 10. macOS Permissions & Environment Setup

| Task | Command | Description |
| ----- | ----- | ----- |
| **Fix BPF Permission Denied (Immediate)** | `sudo chmod 666 /dev/bpf*` | Grants terminal read/write access to capture devices until next reboot |
| **Fix BPF Permissions Permanently** | `brew install --cask wireshark-chmodbpf` | Installs system daemon that sets BPF access automatically on boot |
| **List Hardware Interface Names** | `networksetup -listallhardwareports` | Shows Wi-Fi and Ethernet mappings to system devices (`en0`, `en1`) |