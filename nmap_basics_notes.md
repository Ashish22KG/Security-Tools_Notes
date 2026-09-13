# Nmap: Quick Reference Notes

## 1. Target Specification

| Format | Example | What It Does |
|---|---|---|
| IP range (`-`) | `192.168.0.1-10` | Scans all IPs from .1 to .10 |
| IP subnet (`/`) | `192.168.0.1/24` | Scans the whole subnet (equivalent to `192.168.0.0-255`) |
| Hostname | `example.thm` | Scans by hostname instead of IP |

**Note:** Run Nmap as root/`sudo` to unlock the full range of scan types. As a regular (non-root) user, you're limited to basic scans like ICMP echo and TCP connect scans.

---

## 2. Host Discovery

| Option | What It Does |
|---|---|
| `-sn` | Ping scan — discovers **live hosts only**, without scanning their ports (low-noise recon) |
| `-sL` | List scan — lists the targets that *would* be scanned, without actually scanning them (good for sanity-checking your target range) |

### How Discovery Works

| Network Type | Discovery Method |
|---|---|
| **Local** (same Ethernet/WiFi segment) | Nmap sends **ARP requests**; a response = "Host is up". MAC addresses/vendors are visible since you're on the same L2 segment |
| **Remote** (separated by ≥1 router) | ARP isn't possible. Nmap instead sends a mix of ICMP echo requests, ICMP timestamp requests, TCP SYN packets (commonly to port 443), and TCP ACK packets (commonly to port 80) to infer if a host is alive |

| Advanced Discovery Options | What It Does |
|---|---|
| `-PS[portlist]` | TCP SYN discovery on specified ports |
| `-PA[portlist]` | TCP ACK discovery on specified ports |
| `-PU[portlist]` | UDP discovery on specified ports |

---

## 3. Port Scanning

| Option | What It Does |
|---|---|
| `-sT` | **TCP Connect scan** — completes the full TCP three-way handshake with each target port, then tears the connection down. More "noisy" (fully logged) but doesn't require root |
| `-sS` | **TCP SYN scan ("stealth")** — sends only a SYN packet; never completes the handshake (responds to SYN-ACK with RST instead of ACK). Fewer logs on target, faster, requires root |
| `-sU` | **UDP scan** — probes UDP ports; closed UDP ports typically respond with an ICMP "port unreachable" message |

### Limiting / Selecting Ports

| Option | What It Does |
|---|---|
| `-F` | Fast mode — scans only the 100 most common ports (default is 1000) |
| `-p[range]` | Specifies a port range, e.g. `-p10-1024`, `-p-25` (ports 1–25) |
| `-p-` | Scans **all** 65535 ports (equivalent to `-p1-65535`) |
| `-p1-1023` | Scans just the "well-known" ports (1–1023) |

---

## 4. OS & Service/Version Detection

| Option | What It Does |
|---|---|
| `-O` | **OS detection** — guesses the target's OS family/version based on stack fingerprinting (not 100% accurate) |
| `-sV` | **Service/version detection** — identifies the specific software/version running on each open port (e.g. `OpenSSH 8.9p1`) |
| `-A` | **Aggressive** — combines `-O`, `-sV`, traceroute, and script scanning all in one flag |
| `-Pn` | Treats all hosts as **online** and scans them regardless of host-discovery response — useful when a target doesn't reply to ping/ICMP but is actually up |

---

## 5. Timing & Performance

| Option | What It Does |
|---|---|
| `-T<0-5>` or `-T <name>` | Timing template controlling scan speed: `0` paranoid (slowest, least detectable) → `5` insane (fastest, most detectable). Named: `paranoid`, `sneaky`, `polite`, `normal`, `aggressive`, `insane` |
| `--min-parallelism <n>` / `--max-parallelism <n>` | Sets min/max number of simultaneous probes per host group (auto-tuned by default based on network reliability) |
| `--min-rate <n>` / `--max-rate <n>` | Sets min/max packet-send rate (packets/second) for the **whole scan** |
| `--host-timeout <time>` | Maximum time to wait on a single slow/unresponsive host before moving on |

### Timing Template Reference

| Template | Number | Relative Speed | Use Case |
|---|---|---|---|
| paranoid | `-T0` | Extremely slow (hours) | Maximum stealth, IDS evasion |
| sneaky | `-T1` | Very slow (tens of minutes) | High stealth |
| polite | `-T2` | Slow (tens of seconds) | Reduces load/bandwidth use |
| normal | `-T3` | Default | Standard scans |
| aggressive | `-T4` | Fast | Reliable, fast networks |
| insane | `-T5` | Fastest | Speed over accuracy; may miss results on unreliable networks |

---

## 6. Output & Verbosity

| Option | What It Does |
|---|---|
| `-v` | Verbose output — shows real-time scan stage progress (ARP scan → DNS resolution → SYN scan, etc.). Stack multiple `v`'s (`-vv`, `-vvvv`) or use `-v<level>` (e.g. `-v2`) for more detail. Can also press `v` mid-scan to increase verbosity live |
| `-d` | Debug-level output — much more detailed than `-v`. Stack (`-dd`) or specify a level up to `-d9` (extremely verbose) |

### Saving Scan Reports

| Option | What It Does |
|---|---|
| `-oN <filename>` | Normal (human-readable) output to file |
| `-oX <filename>` | XML output to file |
| `-oG <filename>` | Grepable output to file (useful with `grep`/`awk`) |
| `-oA <basename>` | Saves output in **all three** formats at once (`.nmap`, `.xml`, `.gnmap`) |

---

## 7. Quick Facts / Gotchas

- Nmap defaults to scanning the **1000 most common ports**, not all 65535 — use `-p-` for a full sweep.
- `-sS` (SYN scan) requires root privileges; `-sT` (Connect scan) does not.
- `-Pn` is essential when a target blocks ICMP/ping but you know (or suspect) it's actually up — without it, Nmap will skip port-scanning a host it thinks is down.
- `-A` is convenient but noisy/slow — it bundles multiple detection techniques (OS detection, version detection, traceroute, script scanning).
- Combine scan type + timing + output flags freely, e.g. `nmap -sS -sV -O -T4 -oA results 192.168.1.0/24`.
