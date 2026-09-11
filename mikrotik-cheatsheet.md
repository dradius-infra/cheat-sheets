# MikroTik RouterOS v7 CLI Cheat Sheet

## 1. Safety, Navigation & Maintenance

| Task | Command | Description |
| :--- | :--- | :--- |
| **Toggle Safe Mode** | `<CTRL> + <X>` | Auto-rolls back changes if connection drops |
| **Show Active Interface IPs** | `/ip/address/print` | Lists configured IPs, subnets, and assigned interfaces |
| **Export Sanitized Script** | `/export show-sensitive=no file=backup-clean` | Exports plaintext config without passwords/secrets |
| **Create Full Binary Backup** | `/system/backup/save name=full-backup` | Generates full encrypted binary backup file |
| **Upgrade RouterBOOT Firmware**| `/system/routerboard/upgrade` | Upgrades motherboard BIOS firmware after OS updates |
| **Realtime Resource Monitor** | `/system/resource/monitor` | Live monitoring of CPU per-core usage and RAM |
| **Reboot Router** | `/system/reboot` | Safely reboots the RouterOS device |

---

## 2. L2 Bridge & VLAN Filtering (Bridge VLAN)

| Task | Command | Description |
| :--- | :--- | :--- |
| **List Bridge Ports & PVID** | `/interface/bridge/port/print` | Displays interface-to-bridge mappings and Access PVIDs |
| **Add Access Port to VLAN** | `/interface/bridge/port/add bridge=bridge interface=ether2 pvid=20` | Sets untagged port to a specific VLAN |
| **Add Trunk / Tagged Port** | `/interface/bridge/vlan/add bridge=bridge vlan-ids=10,20 tagged=bridge,ether1` | Tags VLAN traffic on uplink trunk port |
| **Enable VLAN Filtering** | `/interface/bridge/set [find name=bridge] vlan-filtering=yes` | Enforces hardware VLAN tagging on the bridge |
| **Monitor Active Bridge VLANs** | `/interface/bridge/vlan/print` | Displays active VLAN memberships and dynamic ports |

---

## 3. IP Routing, DHCP & DNS

| Task | Command | Description |
| :--- | :--- | :--- |
| **Print Routing Table** | `/ip/route/print` | Displays active, static, and dynamic IPv4 routes |
| **Add Default Gateway** | `/ip/route/add gateway=192.168.1.1` | Sets default egress route (`0.0.0.0/0`) |
| **List Active DHCP Leases** | `/ip/dhcp-server/lease/print` | Shows active clients, assigned IPs, MACs, and hostnames |
| **Make DHCP Lease Static** | `/ip/dhcp-server/lease/make-static numbers=0` | Converts dynamic lease into permanent static entry |
| **Print DNS Cache** | `/ip/dns/cache/print` | Displays locally cached DNS records |
| **Flush Local DNS Cache** | `/ip/dns/cache/flush` | Clears all cached DNS resolutions |
| **Set Upstream DNS Servers** | `/ip/dns/set servers=192.168.1.53,1.1.1.1` | Configures upstream resolvers for the router |

---

## 4. Diagnostics, Tools & Packet Sniffing

| Task | Command | Description |
| :--- | :--- | :--- |
| **Ping Host / Gateway** | `/ping 1.1.1.1 count=5` | Sends ICMP echo requests to verify reachability |
| **Traceroute Target** | `/tool/traceroute 1.1.1.1` | Traces Layer 3 hops to target IP |
| **Discover Connected Neighbors**| `/ip/neighbor/print` | Shows adjacent switches/routers via LLDP/MNDP/CDP |
| **Realtime Packet Sniffer** | `/tool/sniffer/quick interface=ether1 ip-protocol=icmp` | Live captures ICMP packets on physical interface |
| **Torch Interface Traffic** | `/tool/torch interface=ether1 src-address=0.0.0.0/0` | Live per-host bandwidth monitor on selected interface |
| **Bandwidth Test (Local/WAN)**| `/tool/bandwidth-test address=192.168.1.20 user=admin` | Runs MikroTik internal throughput benchmark |

---

## 5. Firewall & Connection Tracking

| Task | Command | Description |
| :--- | :--- | :--- |
| **Print Filter Rules** | `/ip/firewall/filter/print stats` | Lists firewall rules with packet and byte counters |
| **Print Active NAT Rules** | `/ip/firewall/nat/print` | Displays masquerade and port-forwarding mappings |
| **Live Connection Tracking** | `/ip/firewall/connection/print where dst-address~":443"` | Shows open TCP 443 sockets moving across router |
| **Flush Connection Tracking** | `/ip/firewall/connection/remove [find]` | Clears active state table connections |

---

## 6. Modern Wireless / WiFi (RouterOS v7)

| Task | Command | Description |
| :--- | :--- | :--- |
| **Print Wi-Fi Interfaces** | `/interface/wifi/print` | Displays status of 2.4GHz (`wifi1`) and 5GHz (`wifi2`) radios |
| **Show Connected Clients** | `/interface/wifi/registration-table/print` | Lists active wireless devices, signal strength (RSSI) & rates |
| **Scan Surrounding Channels**| `/interface/wifi/scan wifi2` | Scans nearby 5GHz frequencies to find clean channels |
| **Toggle Wi-Fi Radio** | `/interface/wifi/set wifi1 disabled=yes` | Disables 2.4GHz radio |