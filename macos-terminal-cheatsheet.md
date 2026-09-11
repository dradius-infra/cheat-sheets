# macOS Terminal Cheat Sheet

## 1. System Information & Hardware Diagnostics

| Task | Command | Description |
| :--- | :--- | :--- |
| **System Overview** | `system_profiler SPHardwareDataType` | Displays Mac model, CPU/Apple Silicon chip, memory, and serial number |
| **macOS Version & Build** | `sw_vers` | Prints current macOS release, product version, and build number |
| **Kernel Version** | `uname -a` | Displays Darwin kernel release, architecture (`arm64`/`x86_64`), and hostname |
| **Battery Health & Status** | `pmset -g batt` | Shows current charge percentage, power source, and charging state |
| **Thermal & Fan Status** | `sudo powermetrics --samplers smc -n 1` | Probes SMC for CPU/GPU temperatures and active fan speeds |
| **Uptime & System Load** | `uptime` | Displays system runtime duration and 1/5/15-minute load averages |

---

## 2. Process Management & Resource Monitoring

| Task | Command | Description |
| :--- | :--- | :--- |
| **Interactive Process Viewer** | `top -o cpu` | Launches real-time resource monitor ordered by highest CPU utilization |
| **Search Process by Name** | `pgrep -l "ProcessName"` | Finds running process PID and name matching pattern |
| **Kill Process by PID** | `kill -9 <PID>` | Sends `SIGKILL` signal to forcefully terminate a stubborn process |
| **Kill Process by Name** | `killall -9 "ProcessName"` | Forcefully terminates all running instances matching the binary name |
| **Inspect Open Files by App** | `lsof -c "AppName"` | Lists all filesystem handles and files opened by a specific executable |
| **Sample Process Execution** | `sample <PID> 5` | Profiles a hung or slow process for 5 seconds generating a backtrace |

---

## 3. Storage, Volumes & APFS Management

| Task | Command | Description |
| :--- | :--- | :--- |
| **Disk & Partition Layout** | `diskutil list` | Lists all internal/external physical disks, synthesizers, and APFS containers |
| **Filesystem Free Space** | `df -h` | Shows human-readable capacity, used space, and mount paths |
| **Directory Space Breakdown** | `du -sh * \| sort -hr` | Measures directory sizes in current path and sorts largest to smallest |
| **Verify APFS Container** | `diskutil verifyVolume /` | Performs read-only integrity check on the root APFS container |
| **Mount External Volume** | `diskutil mount /dev/disk2s1` | Manually attaches an unmounted drive or partition |
| **Safely Eject Drive** | `diskutil eject /dev/disk2` | Flushes caches, unmounts volumes, and powers down disk spindle/bus |

---

## 4. Permissions, Ownership & macOS Attributes

| Task | Command | Description |
| :--- | :--- | :--- |
| **Change File Permissions** | `chmod 755 script.sh` | Sets Read/Write/Execute for owner, Read/Execute for group and others |
| **Change Ownership** | `sudo chown -R user:staff /path` | Recursively reassigns user and group ownership |
| **View Extended Attributes** | `xattr -l filename` | Lists extended metadata (such as Apple quarantine flags) |
| **Remove Quarantine Flag** | `xattr -d com.apple.quarantine App.app` | Clears Gatekeeper block on binaries downloaded from untrusted sources |
| **Clear All Extended Attributes** | `xattr -c filename` | Strips all ACL tags, quarantine markers, and metadata from file |

---

## 5. macOS Service Control (launchd)

| Task | Command | Description |
| :--- | :--- | :--- |
| **List Loaded User Agents** | `launchctl list` | Prints loaded launchd jobs, PIDs, and last exit statuses |
| **Load User LaunchAgent** | `launchctl load ~/Library/LaunchAgents/job.plist` | Registers and starts a background daemon configuration |
| **Unload LaunchAgent** | `launchctl unload ~/Library/LaunchAgents/job.plist` | Stops running job and removes it from the launchd registry |
| **Kickstart System Daemon** | `sudo launchctl kickstart -k system/com.apple.coreservices` | Force-restarts a native system service by target domain |
| **Inspect Service State** | `launchctl print gui/$(id -u)/com.apple.finder` | Dumps runtime telemetry, environment variables, and state of a target job |

---

## 6. Power, Display & Sleep Configuration

| Task | Command | Description |
| :--- | :--- | :--- |
| **Prevent System Sleep** | `caffeinate -dimsu` | Blocks system, display, and disk sleep while terminal command runs |
| **Caffeinate for Duration** | `caffeinate -u -t 3600` | Forces system to remain awake for specified seconds (e.g., 1 hour) |
| **Show Power Management Settings** | `pmset -g custom` | Displays sleep timeouts, wake-on-LAN settings, and standby triggers |
| **Disable Sleep on AC Power** | `sudo pmset -c sleep 0` | Configures Mac to never sleep when connected to power adapter |
| **Lock Screen Instantly** | `pmset displaysleepnow` | Puts display to sleep immediately and locks user session |

---

## 7. Package Management (Homebrew) & Software Updates

| Task | Command | Description |
| :--- | :--- | :--- |
| **Update Package Metadata** | `brew update` | Fetches latest formula definitions from Homebrew taps |
| **Upgrade Installed Formulae** | `brew upgrade` | Installs new versions of outdated CLI tools and casks |
| **Check for Outdated Packages** | `brew outdated` | Lists local utilities that have newer versions upstream |
| **Audit Homebrew Health** | `brew doctor` | Scans for broken symlinks, compiler mismatches, and path warnings |
| **List Available macOS Updates** | `softwareupdate -l` | Checks Apple CDN servers for pending system and security updates |
| **Install Pending OS Patches** | `sudo softwareupdate -ia --restart` | Downloads and installs all available patches, triggering restart if needed |

---

## 8. Logs, Security & Gatekeeper Administration

| Task | Command | Description |
| :--- | :--- | :--- |
| **Stream Unified System Logs** | `log stream --predicate 'process == "kernel"'` | Streams live unified logging messages filtered by subsystem or process |
| **Collect Diagnostic Sysdiagnose** | `sudo sysdiagnose -f ~/Desktop/` | Compiles deep system performance metrics, core dumps, and event logs |
| **Check Gatekeeper Status** | `spctl --status` | Verifies whether application signature enforcement is enabled |
| **Disable Gatekeeper Temporarily** | `sudo spctl --master-disable` | Re-enables "Anywhere" option in Privacy & Security settings |
| **Verify Code Signing Identity** | `codesign -dv --verbose=4 /path/to/binary` | Validates developer certificate, bundle identifier, and entitlements |

---

## 9. Native GUI Automation, Clipboard & Audio

| Task | Command | Description |
| :--- | :--- | :--- |
| **Pipe to macOS Clipboard** | `cat ~/.ssh/id_ed25519.pub \| pbcopy` | Copies command output directly into the macOS system pasteboard |
| **Paste from Clipboard** | `pbpaste > retrieved-token.txt` | Dumps current pasteboard contents into a terminal stream or file |
| **Trigger macOS Notification** | `osascript -e 'display notification "Task Complete" with title "Terminal"'` | Emits native macOS banner alert via AppleScript wrapper |
| **Text-to-Speech Engine** | `say -v Samantha "System deployment complete"` | Uses native AVFoundation speech synthesizer to speak text aloud |
| **Mute / Unmute System Audio** | `osascript -e "set volume output muted true"` | Programmatically mutes hardware audio output |

---

## 10. Spotlight Indexing & Metadata Search

| Task | Command | Description |
| :--- | :--- | :--- |
| **Fast Metadata Search** | `mdfind -name "config.yaml"` | Uses Spotlight index for instant file finding (faster than `find`) |
| **Search Within File Content** | `mdfind "WireGuard" -onlyin ~/Documents` | Scans inside document contents within a targeted directory |
| **Inspect File Metadata Keys** | `mdls /path/to/file.pdf` | Dumps internal Spotlight attributes (creation apps, codecs, authors) |
| **Check Indexing Status** | `mdutil -s /` | Displays whether Spotlight indexing is enabled on the target volume |
| **Rebuild Spotlight Index** | `sudo mdutil -E /` | Erases and triggers a full metadata re-indexing pass on root volume |

---

## 11. Keychain & Secrets Management

| Task | Command | Description |
| :--- | :--- | :--- |
| **Find Generic Password** | `security find-generic-password -s "ServiceName" -w` | Extracts plaintext secret from user Keychain without launching GUI |
| **Find Internet Password** | `security find-internet-password -s "github.com" -w` | Retrieves stored web/API credentials |
| **List Installed Root Certs** | `security find-certificate -a /Library/Keychains/System.keychain` | Lists system-wide trusted CA certificates |
| **Trust Custom Internal CA** | `sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain cert.crt` | Installs and trusts self-signed CA in macOS System Keychain |
| **Lock Keychain** | `security lock-keychain` | Immediately locks the active user login keychain |