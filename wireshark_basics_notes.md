# Wireshark: The Basics — Quick Reference Notes

## 1. GUI Overview

| Section | Location | What It Does |
|---|---|---|
| Toolbar | Top of window | Menus/shortcuts for sniffing, filtering, sorting, summarizing, exporting, merging |
| Display Filter Bar | Below toolbar | Main query/filtering input for viewing specific packets |
| Recent Files | Start screen | List of recently opened capture files; double-click to reopen |
| Capture Filter & Interfaces | Start screen | Shows available network interfaces (e.g. `lo`, `eth0`, `ens33`) to sniff on, plus capture filters |
| Status Bar | Bottom of window | Shows tool status, active profile, packet counts |

### Packet Detail Panes (after loading a pcap)

| Pane | What It Shows |
|---|---|
| Packet List Pane | One-line summary per packet: source, destination, protocol, info |
| Packet Details Panel | Full protocol breakdown of the selected packet (layer by layer) |
| Packet Bytes Pane | Hex + ASCII view of the selected packet; highlights bytes matching the field clicked in Details pane |

---

## 2. Packet Colouring

| Type | Location | What It Does |
|---|---|---|
| Colouring Rules (permanent) | Right-click menu → Coloring Rules, or **View → Coloring Rules** | Create custom colour rules using display filters; saved to your profile, persist across sessions |
| Colourise Packet List (toggle) | **View → Colourise Packet List** | Turns default/custom colouring on or off |
| Conversation Filter colouring (temporary) | Right-click → Conversation Filter, or **View → Conversation Filter** | Colours only for the current session; not saved |

---

## 3. Traffic Capture / File Management

| Feature | Location | What It Does |
|---|---|---|
| Start Sniffing | Blue "shark fin" button | Begins live packet capture on selected interface |
| Stop Sniffing | Red square button | Stops the active capture |
| Restart Sniffing | Green circular-arrow button | Restarts the capture process |
| Merge PCAP Files | **File → Merge** | Combines a second pcap into the currently open one; must save result afterward |
| View File Details | **Statistics → Capture File Properties** (or click pcap icon, bottom-left) | Shows file hash, capture time, file comments, interface used, and stats — useful for identifying/classifying files |

---

## 4. Packet Navigation

| Feature | Location | What It Does |
|---|---|---|
| Packet Numbers | Packet List Pane (leftmost column) | Unique sequential ID per packet; makes it easy to reference/return to specific packets |
| Go to Packet | **Go** menu / toolbar | Jump directly to a specific packet number or the next packet in a conversation |
| Find Packet | **Edit → Find Packet** | Searches packet content by Display Filter, Hex, String, or Regex; choose which pane (List/Details/Bytes) to search in — search only works if the term exists in the pane you selected |
| Mark Packet | **Edit → Mark/Unmark Packet**, or right-click | Flags a packet (shown in black) for attention/export; marks are lost when the file is closed |
| Packet Comments | Right-click → Packet Comment (or Edit menu) | Adds a note to a specific packet; comments are saved *inside* the capture file (persist across sessions, unlike marks) |

---

## 5. Exporting

| Feature | Location | What It Does |
|---|---|---|
| Export Packets | **File → Export Specified Packets** | Saves a selected subset of packets to a new pcap (e.g., only suspicious ones) |
| Export Objects (Files) | **File → Export Objects → (DICOM / HTTP / IMF / SMB / TFTP)** | Extracts files that were transferred over the wire in the capture, for those specific protocols only |

---

## 6. Time & Diagnostics

| Feature | Location | What It Does |
|---|---|---|
| Time Display Format | **View → Time Display Format** | Changes timestamp display (default: "Seconds Since Beginning of Capture") — commonly switched to UTC for readability |
| Expert Information | Bottom-left status bar icon, or **Analyze → Expert Information** | Flags possible anomalies/errors in protocols. Severities: **Chat** (blue, normal workflow info), **Note** (cyan, notable events like error codes), **Warn** (yellow, unusual errors/problems), **Error** (red, malformed packets). Common groups: Checksum, Comment, Deprecated, Malformed |

---

## 7. Packet Filtering

| Feature | Location | What It Does |
|---|---|---|
| Apply as Filter | Right-click a field → Apply as Filter, or **Analyze → Apply as Filter** | Instantly filters the view to only packets matching that single field's value |
| Conversation Filter | Right-click → Conversation Filter, or **Analyze → Conversation Filter** | Filters to show only packets belonging to a specific conversation (same IP/port pair) |
| Colourise Conversation | Right-click → Colourise Conversation, or **View → Colourise Conversation** | Highlights a conversation's packets without hiding others (no filter applied); undo via **View → Colourise Conversation → Reset Colourisation** |
| Prepare as Filter | Right-click → Prepare as Filter | Builds the filter query in the filter bar but does **not** execute it — lets you combine with `and`/`or` before hitting Enter |
| Apply as Column | Right-click a field → Apply as Column, or **Analyze → Apply as Column** | Adds that field as a new column in the Packet List Pane for quick scanning across all packets |
| Follow Stream (TCP/UDP/HTTP) | Right-click → Follow → TCP/UDP/HTTP Stream, or **Analyze → Follow → TCP/UDP/HTTP Stream** | Reconstructs the full conversation at the application layer (e.g., plaintext creds, page content). Server data shown in blue, client data in red. Auto-applies a stream filter — clear it with the **X** on the filter bar to see all packets again |

### Basic Display Filter Syntax

| Filter | Location | What It Does |
|---|---|---|
| `http`, `arp`, `dhcp`, `ftp`, `smtp`, `pop`, `imap`, etc. | Display Filter Bar | Filters by protocol name |
| `tcp.port == <port>` / `udp.port == <port>` | Display Filter Bar | Filters by port number (e.g. `tcp.port == 80` for HTTP) |
| `ip.addr == <IP address>` | Display Filter Bar | Filters all traffic to/from a specific IP address |

**Note:** Capture filters = filter *while capturing* (only matching packets get recorded). Display filters = filter *after capture* (all packets are stored, only matching ones are shown).

---

## 8. Packet Dissection — OSI Layers in a Packet

When you click a packet in the Packet List Pane, the Packet Details Panel breaks it into layers:

| Layer (in Wireshark) | OSI Layer | What It Shows |
|---|---|---|
| Frame | Physical | Frame/packet number and physical-layer capture metadata |
| Source [MAC] | Data Link | Source & destination MAC addresses |
| Source [IP] | Network | Source & destination IPv4 addresses |
| Protocol (TCP/UDP) | Transport | Protocol used, source & destination ports |
| Protocol Errors | Transport (cont.) | TCP segments that needed reassembly |
| Application Protocol | Application | Protocol-specific details (HTTP, FTP, SMB, etc.) |
| Application Data | Application (cont.) | Actual application-layer payload/data |

---

## 9. Quick Facts / Gotchas

- Wireshark is **not an IDS** — it doesn't detect threats automatically or modify packets; it only reads and displays them. Detection depends on analyst skill.
- Export Objects only works for **DICOM, HTTP, IMF, SMB, TFTP** streams — not all protocols.
- **Marks** = temporary (lost on file close). **Comments** = permanent (saved in the pcap file).
- Searching in **Find Packet** only works if you pick the correct pane (List/Details/Bytes) matching where the data actually appears.
