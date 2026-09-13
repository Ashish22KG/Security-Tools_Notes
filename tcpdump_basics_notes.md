# Tcpdump: Quick Reference Notes

## 1. Core Capture Options

| Option | Example | What It Does |
|---|---|---|
| `-i INTERFACE` | `tcpdump -i eth0` | Captures on a specific network interface. Use `-i any` to listen on all interfaces |
| `-w FILE` | `tcpdump -w data.pcap` | Writes captured packets to a file (usually `.pcap`) instead of printing to screen — nothing scrolls on screen while this runs |
| `-r FILE` | `tcpdump -r traffic.pcap` | Reads/replays packets from a previously saved capture file (no root needed for this) |
| `-c COUNT` | `tcpdump -c 50` | Limits capture to a specific number of packets; without it, capture runs until you press `CTRL-C` |
| `-n` | `tcpdump -n` | Disables DNS resolution — shows IP addresses only, not hostnames |
| `-nn` | `tcpdump -nn` | Disables **both** DNS resolution and port-name resolution (e.g., shows `80` instead of `http`) |
| `-v` / `-vv` / `-vvv` | `tcpdump -v` | Verbose output; more `v`'s = more detail (TTL, ID, total length, options, etc.) |

**Finding interfaces:** `ip a s` (or `ip address show`) lists available interfaces (e.g. `lo`, `eth0`, `ens5`).

---

## 2. Filtering Expressions

### By Host

| Filter | What It Does |
|---|---|
| `host IP` or `host HOSTNAME` | Captures all packets to/from that host |
| `src host IP`/`HOSTNAME` | Captures packets **from** that source host only |
| `dst host IP`/`HOSTNAME` | Captures packets **to** that destination host only |

### By Port

| Filter | What It Does |
|---|---|
| `port PORT_NUMBER` | Captures packets to/from that port (e.g., `port 53` for DNS) |
| `src port PORT_NUMBER` | Captures packets from that source port only |
| `dst port PORT_NUMBER` | Captures packets to that destination port only |

### By Protocol

| Filter | What It Does |
|---|---|
| `ip`, `ip6`, `tcp`, `udp`, `icmp`, etc. | Limits capture to that specific protocol |

### Logical Operators

| Operator | Example | What It Does |
|---|---|---|
| `and` | `host 1.1.1.1 and tcp` | Captures packets matching **both** conditions |
| `or` | `udp or icmp` | Captures packets matching **either** condition |
| `not` | `not tcp` | Captures packets that do **not** match the condition |

### Example Commands

| Command | What It Does |
|---|---|
| `tcpdump -i eth0 -c 50 -v` | Captures 50 packets on `eth0`, displays verbosely |
| `tcpdump -i wlo1 -w data.pcap` | Captures on WiFi interface `wlo1`, writes to file until interrupted |
| `tcpdump -i any -nn` | Captures on all interfaces, no DNS/port resolution |
| `tcpdump -i any tcp port 22` | Captures SSH traffic on all interfaces |
| `tcpdump -i wlo1 udp port 123` | Captures NTP traffic on WiFi interface |
| `tcpdump -i eth0 host example.com and tcp port 443 -w https.pcap` | Captures HTTPS traffic to/from example.com on `eth0`, saves to file |
| `tcpdump -r traffic.pcap -c 5 -n` | Reads first 5 packets from a file, no IP resolution |
| `tcpdump -r traffic.pcap src host 192.168.124.1 -n \| wc` | Reads from file, filters by source host, pipes to `wc` to count lines |

---

## 3. Output Display Options

| Option | What It Does |
|---|---|
| `-q` | **Quick** output — brief packet info only (timestamp, src/dst IP:port, protocol, length) |
| `-e` | Includes the **link-level header** (source/destination MAC addresses) — useful for ARP/DHCP analysis or tracking rogue devices |
| `-A` | Displays packet data in **ASCII** — useful for reading plaintext protocol content (e.g., HTTP) |
| `-xx` | Displays packet data in **hexadecimal** — useful when content is encrypted, compressed, or non-English (can't be read as ASCII) |
| `-X` | Displays packet data in **both hex and ASCII** side by side — best of both worlds |

---

## 4. Quick Facts / Gotchas

- Capturing **live traffic** requires root privileges (`sudo`); reading from a saved file with `-r` does **not**.
- `-w` writes raw output to a file and suppresses on-screen packet display.
- `-n` vs `-nn`: `-n` skips DNS lookups only; `-nn` skips DNS **and** port-name lookups.
- Combine capture + display + filter options freely, e.g. `sudo tcpdump -i ens5 -c 5 -n port 53`.
- Use `wc` (word count) to pipe output and quickly count matching packets, e.g. `tcpdump -r file.pcap src host X -n | wc`.
