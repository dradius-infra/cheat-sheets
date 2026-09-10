# Proxmox VE CLI Cheat Sheet

## 1. Virtual Machines Management (qm)

| Task | Command | Description |
| :--- | :--- | :--- |
| **List All VMs** | `qm list` | Displays VM ID, name, status, memory, and uptime |
| **Start / Stop VM** | `qm start 100` / `qm stop 100` | Powers on or cleanly ACPI shuts down target VM |
| **Force Stop VM** | `qm stop 100 --skiplock 1` | Kills a frozen VM bypassing lock states |
| **Open VM Console** | `qm terminal 100` | Attaches directly to the serial terminal of the VM |
| **Clone VM Template** | `qm clone 9000 101 --name docker-node` | Full clones a template into a new VM instance |
| **Resize VM Disk** | `qm resize 100 scsi0 +20G` | Extends virtual disk capacity by 20GB |
| **Unlock Locked VM** | `qm unlock 100` | Clears backup or snapshot locks preventing actions |

---

## 2. LXC Containers Management (pct)

| Task | Command | Description |
| :--- | :--- | :--- |
| **List All LXCs** | `pct list` | Displays LXC ID, status, IP, and resource allocations |
| **Start / Stop LXC** | `pct start 200` / `pct shutdown 200` | Boots or gracefully shuts down target container |
| **Instant Shell Access** | `pct enter 200` | Drops directly into container root shell without SSH |
| **Run Remote Command** | `pct exec 200 -- apt update` | Executes a command inside the container from host |
| **Pull Container Config** | `pct config 200` | Displays raw hardware, mount points, and network setup |
| **Resize LXC Disk** | `pct resize 200 rootfs 16G` | Expands container root filesystem size |
| **Unlock Locked LXC** | `pct unlock 200` | Removes stuck lock state after failed operations |

---

## 3. Storage & Backup Operations (pvesm & vzdump)

| Task | Command | Description |
| :--- | :--- | :--- |
| **List Storage Status** | `pvesm status` | Shows storage pools, types (ZFS/LVM/NFS), and free space |
| **List ISOs & Images** | `pvesm list local` | Lists stored ISOs, backup dumps, and container templates |
| **Instant LXC/VM Backup** | `vzdump 100 --compress zstd --storage local` | Creates live compressed backup snapshot to local storage |
| **Restore Container** | `pct restore 200 /var/lib/vz/dump/vzdump-lxc-200.tar.zst --storage local-lvm` | Restores container from specific backup archive |
| **Restore VM** | `qmrestore /var/lib/vz/dump/vzdump-qemu-100.vma.zst 100` | Restores full virtual machine from backup file |

---

## 4. Host Networking & Linux Bridges

| Task | Command | Description |
| :--- | :--- | :--- |
| **Show Network Status** | `ip -c addr` | Color-coded display of interfaces, bridges (`vmbr0`), and IPs |
| **Inspect Linux Bridge** | `bridge link show` | Lists physical NICs and tap/veth devices attached to bridges |
| **Live Reload Network** | `ifreload -a` | Reloads `/etc/network/interfaces` without rebooting PVE host |
| **Test Host Port Reachability**| `nc -zv 192.168.1.1 22` | Verifies host uplink connection to core gateway |
| **Trace Host Routing** | `ip route show` | Displays active kernel routing table and default gateway |

---

## 5. Host Health, Hardware & Troubleshooting

| Task | Command | Description |
| :--- | :--- | :--- |
| **Cluster / Node Status** | `pvecm status` | Displays cluster health or standalone node status |
| **Hardware Temperatures** | `sensors` | Reads CPU cores and system package temperatures |
| **Realtime Resource Monitor**| `htop` | Live view of CPU threads, RAM usage, and processes |
| **Follow PVE Task Logs** | `journalctl -u pveproxy -f` | Live streams API and Web GUI access logs |
| **Tail System Events** | `dmesg -T \| grep -iE "error\|fail"` | Filters kernel logs for hardware or drive failure events |
| **Restart Core PVE Services**| `systemctl restart pvedaemon pveproxy` | Restarts management backend and Web UI daemon |

---

## 6. Snapshots & Disaster Rollback

| Task | Command | Description |
| :--- | :--- | :--- |
| **Create VM Snapshot** | `qm snapshot 100 pre-update --vmstate 1` | Takes snapshot including RAM state |
| **List VM Snapshots** | `qm listsnapshot 100` | Displays snapshot tree and timestamps |
| **Rollback VM** | `qm rollback 100 pre-update` | Restores VM state back to snapshot point |
| **Delete VM Snapshot** | `qm delsnapshot 100 pre-update` | Merges and deletes snapshot delta disk |
| **Create LXC Snapshot** | `pct snapshot 200 clean-state` | Takes storage snapshot of container |
| **Rollback LXC** | `pct rollback 200 clean-state` | Instantly reverts container back to snapshot |

---

## 7. Physical Disks & Hardware Health (SMART)

| Task | Command | Description |
| :--- | :--- | :--- |
| **List Physical Disks** | `lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINT` | Shows block device tree and partition mounts |
| **Disk NVMe Health Check** | `smartctl -a /dev/nvme0` | Checks NVMe wearout percentage and error log |
| **Disk SATA/SSD Health Check**| `smartctl -H /dev/sda` | Quick PASS/FAIL health verification |
| **Check SSD Wearout Life** | `smartctl -A /dev/nvme0 \| grep -i percentage` | Displays remaining SSD/NVMe endurance life percentage |