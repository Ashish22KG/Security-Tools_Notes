# John the Ripper: Quick Reference Notes

## 1. Basic Syntax

```
john [options] [file path]
```

| Part | What It Means |
|---|---|
| `john` | Invokes John the Ripper |
| `[options]` | Flags controlling mode, format, wordlist, etc. |
| `[file path]` | File containing the hash(es) to crack |

---

## 2. Core Cracking Modes

| Command | Mode | What It Does |
|---|---|---|
| `john --wordlist=<path> <file>` | Wordlist / Automatic | Tries every word in the wordlist against the hash. John auto-detects hash type (not always reliable) |
| `john --format=<format> --wordlist=<path> <file>` | Format-specific | Same as above but forces a specific hash format instead of relying on auto-detection |
| `john --single --format=<format> <file>` | Single Crack | Generates candidate passwords by "mangling" the **username** itself (e.g. `Markus` → `Markus1`, `MArkus`, `Markus!`). Requires the file to be formatted as `username:hash` |
| `john --list=formats` | — | Lists all supported hash formats (pipe to `grep -iF "<type>"` to search) |

**Note on formats:** standard hash types (e.g. `md5`) usually need a `raw-` prefix (`raw-md5`) — check with `--list=formats` if unsure.

---

## 3. Identifying Unknown Hashes

| Tool | What It Does |
|---|---|
| Online hash identifier sites | Paste a hash, get likely format guesses |
| `hash-identifier` (Python tool) | Download via `wget`/`curl`, run with `python3 hash-id.py`, paste hash → lists probable formats |

---

## 4. Word Mangling & GECOS (Single Crack Mode)

- **Word mangling** = John mutates a base word (like a username) into likely password variants (capitalization, appended numbers/symbols).
- **GECOS field** = the 5th field in `/etc/passwd` (full name, office info, etc.). John can pull this info automatically to expand its mangled wordlist when cracking `/etc/shadow` hashes in single crack mode.
- Single crack mode requires input formatted as `username:hash` so John knows what word to mangle.

---

## 5. Custom Rules

Defined in `john.conf`:

| OS install method | Config file location |
|---|---|
| TryHackMe AttackBox | `/opt/john/john.conf` |
| Package manager / built from source | `/etc/john/john.conf` |

### Rule Syntax

| Element | What It Does |
|---|---|
| `[List.Rules:RuleName]` | Names your custom rule (used later with `--rule=RuleName`) |
| `c` | Capitalizes a character positionally |
| `Az"..."` | Appends the given characters to the end of the word |
| `A0"..."` | Prepends the given characters to the start of the word |
| `[0-9]` | Any digit 0–9 |
| `[A-z]` | Upper and lowercase letters |
| `[A-Z]` | Uppercase only |
| `[a-z]` | Lowercase only |
| `[a]` | Only the literal character `a` |
| `[!£$%@]` | Any of the listed symbols |

**Example rule** (matches pattern like `Polopassword1!`):
```
[List.Rules:PoloPassword]
cAz"[0-9] [!£$%@]"
```
`c` = capitalize first letter, `Az` = append, `[0-9]` = a digit, `[!£$%@]` = a symbol.

**Using it:**
```
john --wordlist=<path> --rule=PoloPassword <file>
```

**What custom rules exploit:** predictable password-complexity patterns — users tend to satisfy complexity rules (upper/lower/number/symbol) in the same predictable positions (capital first letter, number + symbol at the end).

Jumbo John ships with an extensive built-in rule list (~line 678 in `john.conf`) worth checking if your own rule syntax isn't working.

---

## 6. Cracking Real-World Hash Types

### Windows NTHash / NTLM

| Fact | Detail |
|---|---|
| What it is | Modern Windows password hash format (successor to LM, hence "NT/LM") |
| Where it comes from | SAM database (via tools like Mimikatz) or `NTDS.dit` (Active Directory) |
| Format flag | `--format=nt` |
| Alternative to cracking | Pass-the-hash attack (no cracking needed) |

### Linux `/etc/shadow`

| Step | Command | What It Does |
|---|---|---|
| 1. Combine passwd + shadow | `unshadow <passwd file> <shadow file> > unshadowed.txt` | Merges the two files into a format John can read (John needs both to understand the data) |
| 2. Crack | `john --wordlist=<path> --format=sha512crypt unshadowed.txt` | Cracks the combined file (format depends on the hash algorithm used, e.g. `sha512crypt`) |

---

## 7. File & Key Cracking (Conversion Tools)

All of these follow the same pattern: **convert → pipe into John with a wordlist.**

| Target | Conversion Tool | Command |
|---|---|---|
| ZIP archive | `zip2john` | `zip2john <zipfile> > zip_hash.txt` |
| RAR archive | `rar2john` | `rar2john <rarfile> > rar_hash.txt` |
| SSH private key (`id_rsa`) | `ssh2john` (or `ssh2john.py`) | `ssh2john <id_rsa> > id_rsa_hash.txt` |

Then, for all three:
```
john --wordlist=/usr/share/wordlists/rockyou.txt <output_hash_file>
```

**Note:** if `ssh2john` isn't installed directly, use the Python script: `python3 /opt/john/ssh2john.py` (AttackBox) or `python /usr/share/john/ssh2john.py` (Kali).

---

## 8. Quick Facts / Gotchas

- Automatic hash-type detection is convenient but **unreliable** — prefer `--format=` when you know the hash type.
- Single crack mode needs `username:hash` formatting; wordlist mode does not.
- `unshadow`, `zip2john`, `rar2john`, and `ssh2john` all exist purely to **reformat** target data into something John can natively ingest — the actual crack always ends with a normal `john --wordlist=...` command.
- Custom rules live in `john.conf` and are invoked with `--rule=<RuleName>`.
