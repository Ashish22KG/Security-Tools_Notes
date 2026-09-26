# Burp Suite Basics — TryHackMe Room Notes

## Feature / Sub-option Reference Table

| Burp Suite Feature | Sub-option | Description |
|---|---|---|
| **Proxy** | Intercept | Captures and holds requests/responses between browser and server before they reach their destination; can be toggled on/off ("Intercept is on/off"). |
| | HTTP history | Log of all requests/responses that passed through the proxy, even when intercept is off — useful for retrospective review. |
| | WebSockets history | Logs WebSocket communication captured by the proxy, separate from regular HTTP history. |
| | Response Interception | Proxy setting to also intercept server responses (off by default) based on defined rules. |
| | Match and Replace | Proxy setting using regex to automatically modify incoming/outgoing requests (e.g., changing user-agent, manipulating cookies). |
| | Open Browser (Burp Browser) | Built-in Chromium browser pre-configured to route traffic through the proxy without manual browser setup. |
| **Repeater** | — | Captures, modifies, and resends the same request multiple times — useful for crafting payloads via trial and error (e.g., SQLi) or testing endpoint behavior. |
| **Intruder** | — | Sends automated/spray requests to endpoints — commonly used for brute-forcing or fuzzing (rate-limited in Community edition). |
| **Decoder** | — | Decodes captured data or encodes payloads before sending to the target (e.g., URL encoding). |
| **Comparer** | — | Compares two pieces of data at the word or byte level. |
| **Sequencer** | — | Assesses the randomness/entropy of tokens such as session cookies, to check for insecure generation algorithms. |
| **Target** | Site map | Tree-structure map of the target web app, auto-populated as pages are visited through the proxy; useful for enumeration and API endpoint discovery. |
| | Issue definitions | Reference list of web vulnerabilities (with descriptions/references) that Burp's scanner checks for — available even without the Professional scanner. |
| | Scope settings | Defines which domains/IPs are in-scope for testing, so unrelated traffic isn't logged/intercepted. |
| **Dashboard** | Tasks | Defines background tasks Burp runs while in use (e.g., default "Live Passive Crawl" in Community). |
| | Event log | Shows actions performed by Burp Suite (e.g., starting the proxy) and connection details. |
| | Issue Activity | (Professional only) Displays vulnerabilities found by the automated scanner, ranked by severity. |
| | Advisory | (Professional only) Detailed info on identified vulnerabilities, including references/remediations, exportable to a report. |
| **Settings** | User settings (Global) | Settings that apply to the entire Burp Suite installation, persisting across sessions. |
| | Project settings | Settings specific to the current project/session only (not saved in Community edition). |
| | Search | Lets you search all settings by keyword. |
| | Type filter | Filters settings by User vs Project type. |
| | Categories | Browse settings grouped by category (e.g., Sessions, Suite, Hotkeys). |
| **Extender / BApp Store** | — | Allows loading extensions (written in Java, Python via Jython, or Ruby via JRuby) to add functionality; BApp Store is the marketplace for third-party extensions (e.g., Logger++). |

## Editions Comparison

| Edition | Key Characteristics |
|---|---|
| Community | Free; core manual testing tools; rate-limited Intruder; no project saving; limited extensions. |
| Professional | Unrestricted Intruder, automated vulnerability scanner, project saving/reporting, built-in API, unrestricted extensions, Burp Collaborator access. |
| Enterprise | Server-based, continuous/automated scanning of web apps (similar to Nessus for infrastructure) rather than manual local testing. |

## Short Notes

- **Core function**: Burp Suite is a Java-based framework that intercepts and allows manipulation of HTTP/HTTPS traffic between browser and server — this interception capability is the backbone of the whole framework.
- **Keyboard shortcuts**: `Ctrl+Shift+D` = Dashboard, `Ctrl+Shift+T` = Target, `Ctrl+Shift+P` = Proxy, `Ctrl+Shift+I` = Intruder, `Ctrl+Shift+R` = Repeater.
- **Proxy setup with a regular browser**: Requires an extension like FoxyProxy configured to route traffic to `127.0.0.1:8080` (Burp's default listening address), plus Burp's Intercept toggled on to capture requests.
- **TLS/HTTPS interception**: Browsers won't trust Burp's certificate by default. Fix by downloading the PortSwigger CA cert from `http://burp/cert` (while proxy is active) and importing/trusting it in the browser's certificate manager.
- **Scoping matters**: Without scoping, Burp logs and intercepts *all* traffic, which gets overwhelming. Add targets to scope (Target tab → right-click → Add to Scope) and enable "And URL Is in target scope" under Proxy settings → Intercept Client Requests, so only relevant traffic is captured.
- **Burp Browser sandbox issue on Linux/root**: Running as root (as on the AttackBox) can block the Burp Browser's sandbox. Fix by either running Burp under a low-privilege user, or enabling "Allow Burp's browser to run without a sandbox" in Settings → Tools (use cautiously — this reduces browser security).
- **Client-side filters are not real security**: As shown with the XSS walkthrough, client-side input filters (e.g., blocking special characters in an email field) can be bypassed by intercepting the request in the proxy and editing the raw payload directly before forwarding it — a good reminder that all validation must also be enforced server-side.
- **URL encoding a payload**: In Burp's proxy/Repeater, select the payload text and press `Ctrl+U` to URL-encode it before forwarding, so special characters are transmitted safely.
