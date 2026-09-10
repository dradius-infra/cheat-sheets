# macOS Network & CLI Cheat Sheet

## 1. Local Network & Interface Status

| Task | Command | Description |
| :--- | :--- | :--- |
| **Wi-Fi IPv4 Address** | `ipconfig getifaddr en0` | Returns only the active Wi-Fi IPv4 address |
| **Detailed Interface Info** | `ifconfig en0` | Displays subnet mask, broadcast, MTU, and MAC address |
| **List All Hardware Ports** | `networksetup -listallhardwareports` | Maps physical ports to logical interface names (`en0`, `en1`) |
| **Interface Link Statistics** | `netstat -I en0 -b` | Displays packets and bytes transferred/received |
| **Renew DHCP Lease** | `sudo ipconfig set en0 DHCP` | Triggers a fresh DHCP request on the local interface |

---

## 2. Routing, ARP & Gateways

| Task | Command | Description |
| :--- | :--- | :--- |
| **Show Default Gateway** | `route -n get default` | Displays the gateway IP and the active egress interface |
| **Full IPv4 Routing Table** | `netstat -nr -f inet` | Prints the complete IPv4 routing table |
| **View ARP Cache** | `arp -a` | Shows IP-to-MAC address mappings on the local L2 segment |
| **Delete Single ARP Entry** | `sudo arp -d 192.168.1.1` | Clears a specific stale MAC entry |
| **Flush Full ARP Table** | `sudo arp -a -d` | Clears the entire local ARP cache |

---

## 3. Port Diagnostics & Traffic Flow

| Task | Command | Description |
| :--- | :--- | :--- |
| **All Listening TCP Ports** | `lsof -nP -iTCP -sTCP:LISTEN` | Lists processes listening on open TCP ports |
| **Inspect Specific Port** | `lsof -nP -i :53` | Checks which process is bound to a specific port |
| **Quick TCP Port Probe** | `nc -zv -w 2 192.168.1.1 22` | Tests port reachability without establishing an SSH session |
| **Packet Sniffing (CLI)** | `sudo tcpdump -ni en0 icmp` | Captures live ICMP packets on the Wi-Fi interface |
| **Realtime Socket Stats** | `netstat -anv -p tcp` | Displays current state of all active TCP sockets |

---

## 4. DNS Inspection & Cache Management

| Task | Command | Description |
| :--- | :--- | :--- |
| **Query Specific DNS Server** | `dig @10.0.0.53 service.local` | Queries internal DNS directly, bypassing system resolvers |
| **Short IP Resolution** | `dig +short google.com` | Returns IP addresses only, stripping extra metadata |
| **Reverse DNS Lookup** | `dig -x 192.168.1.1 +short` | Queries the PTR record for a given IP address |
| **Flush macOS DNS Cache** | `sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder` | Clears the local macOS DNS cache |
| **Show Current DNS Resolvers** | `scutil --dns` | Lists active DNS servers per domain and network service |

---

## 5. Wi-Fi Diagnostics & Native Network Setup

| Task | Command | Description |
| :--- | :--- | :--- |
| **Current Wi-Fi Details** | `system_profiler SPAirPortDataType` | Shows SSID, BSSID, RSSI, Noise, Channel, and Tx Rate |
| **Cycle Wi-Fi Interface** | `networksetup -setairportpower en0 off && networksetup -setairportpower en0 on` | Restarts the Wi-Fi hardware adapter |
| **List Saved Networks** | `networksetup -listpreferredwirelessnetworks en0` | Displays preferred Wi-Fi networks |
| **Set Static IP on Wi-Fi** | `networksetup -setmanual "Wi-Fi" 192.168.1.50 255.255.255.0 192.168.1.1` | Configures static IP, subnet mask, and gateway |
| **Revert Wi-Fi to DHCP** | `networksetup -setdhcp "Wi-Fi"` | Switches interface back to automatic DHCP addressing |

---

## 6. Advanced Diagnostics & Path MTU

| Task | Command | Description |
| :--- | :--- | :--- |
| **Trace Route (ICMP)** | `traceroute -I 1.1.1.1` | Uses ICMP echo instead of default UDP probes |
| **Trace Route (TCP Port)** | `traceroute -P TCP -p 443 1.1.1.1` | Traces route using TCP SYN to test firewall drops |
| **Test MTU Size / No-Frag** | `ping -D -s 1472 192.168.1.1` | Sends 1500-byte frame (1472 payload + 28 header) with DF flag |
| **IPv6 Neighbor Discovery** | `ndp -a` | Displays IPv6-to-MAC neighbor table (IPv6 equivalent of ARP) |
| **Ping Subnet Broadcast** | `ping -c 3 192.168.1.255` | Pings the subnet broadcast address to discover active hosts |

---

## 7. HTTP/API & Layer 7 Troubleshooting

| Task | Command | Description |
| :--- | :--- | :--- |
| **Inspect HTTP Headers** | `curl -Iv https://192.168.1.1` | Shows TLS handshake and response headers without body |
| **Resolve Override (Test DNS)** | `curl -Iv --resolve service.local:443:10.0.0.10 https://service.local` | Forces curl to hit a specific IP ignoring public/local DNS |
| **Measure Connection Timings** | `curl -w "DNS: %{time_namelookup}s \| Connect: %{time_connect}s \| Total: %{time_total}s\n" -o /dev/null -s https://google.com` | Prints exact DNS lookup, TCP handshake, and TLS timings |

---

## 8. Deep macOS Wi-Fi & System Hardware

| Task | Command | Description |
| :--- | :--- | :--- |
| **Scan Nearby BSSIDs & Channels** | `sudo /System/Library/PrivateFrameworks/Apple80211.framework/Resources/airport -s` | Scans surrounding SSIDs, RSSI, channels, and channel widths |
| **Show Default Gateway Interface** | `route get default \| grep interface` | Identifies exact active physical interface routing traffic |
| **List Interface Speed/Duplex** | `ifconfig en0 media` | Displays physical link rate (e.g., 1000baseT, full-duplex) |

---

## 9. Essential External Network Tools (Homebrew)

### Installation & Usage Matrix

| Tool | Install Command | Run Command | Description |
| :--- | :--- | :--- | :--- |
| **iperf3 (Client)** | `brew install iperf3` | `iperf3 -c 192.168.1.10 -P 4` | Tests bandwidth throughput with 4 parallel streams |
| **iperf3 (Server)** | `brew install iperf3` | `iperf3 -s` | Starts local listener to benchmark incoming speed |
| **mtr** | `brew install mtr` | `sudo mtr -rw 1.1.1.1` | Live trace combining ping and packet loss stats per hop |
| **nmap (Port Scan)** | `brew install nmap` | `nmap -sS -p 22,80,443 192.168.1.0/24` | SYN scans common administrative ports across a subnet |
| **nmap (Fingerprint)**| `brew install nmap` | `nmap -sV -O 192.168.1.1` | Identifies OS and software versions running on open ports |
| **wakeonlan** | `brew install wakeonlan` | `wakeonlan 02:42:AC:11:00:02` | Sends magic packets to wake sleeping hosts via MAC |