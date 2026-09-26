# Hydra — TryHackMe Room Notes

## Command / Option Reference Table

| Option / Syntax | Example | Description |
|---|---|---|
| `-l` | `-l user` | Specifies a single username to use for login attempts. |
| `-P` | `-P passlist.txt` | Specifies a wordlist file of passwords to try. |
| `-t` | `-t 4` | Sets the number of parallel threads to spawn (speeds up the attack). |
| `-s` | `-s <port>` | Specifies a custom port number if the service isn't listening on its default port. |
| `-V` | `-V` | Verbose mode — shows output for every login attempt made. |
| `http-post-form` | `http-post-form "<path>:<login_credentials>:<invalid_response>"` | Tells Hydra the target is a web login form using the POST method. |
| `^USER^` | `username=^USER^` | Placeholder in the form string that Hydra replaces with each username being tried. |
| `^PASS^` | `password=^PASS^` | Placeholder in the form string that Hydra replaces with each password being tried. |
| `F=<string>` | `F=incorrect` | Defines a string that appears in the server's response when login **fails** — used to identify unsuccessful attempts. |
| Basic syntax | `hydra -l user -P passlist.txt ftp://MACHINE_IP` | General Hydra structure: `hydra [options] [target] [protocol]`. |
| SSH syntax | `hydra -l <username> -P <full_path_to_wordlist> MACHINE_IP -t 4 ssh` | Brute-forces SSH login using a given username and password list. |
| Web form (POST) syntax | `hydra -l <username> -P <wordlist> MACHINE_IP http-post-form "/:username=^USER^&password=^PASS^:F=incorrect" -V` | Brute-forces a POST-based web login form. |

## Short Notes

- **What Hydra is**: An online/network brute-force password cracking tool used to speed up guessing login credentials against services like SSH, FTP, web forms, and many others, rather than trying passwords manually.
- **Wide protocol support**: Hydra can target a large range of protocols/services (FTP, SSH, HTTP/HTTPS forms, SMB, RDP, MySQL, SNMP, Telnet, VNC, and many more) — see the official Hydra repo or Kali's Hydra tool page for full protocol-specific options.
- **Why strong passwords matter**: Short, common passwords (under 8 characters, no special characters) are highly vulnerable to brute-force/wordlist attacks — large leaked password lists (e.g., 100 million common passwords) make this easy for tools like Hydra.
- **Default credentials risk**: Devices like CCTV cameras and web frameworks often ship with weak default logins (e.g., `admin:password`) — these should always be changed immediately after setup.
- **Identifying the web form type**: Before brute-forcing a web login, check whether it uses GET or POST — this can be found via the browser's Developer Tools → Network tab, or by viewing the page source.
- **Building the POST form string**: The `http-post-form` string has three parts separated by colons — the login page path, the POST body with `^USER^`/`^PASS^` placeholders, and a failure-condition string (`F=...`) so Hydra knows which attempts failed.
- **Threads (`-t`) trade-off**: More threads = faster brute-forcing, but too many can overload the target service or trigger lockouts/rate limiting.
