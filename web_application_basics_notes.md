# Web Application Basics: Quick Reference Notes

## 1. Web Application Components

| Component | Layer | What It Does |
|---|---|---|
| HTML | Front End | Defines the structure/content the browser displays |
| CSS | Front End | Defines appearance — colors, layout, typography |
| JavaScript | Front End | Adds interactivity/logic — dynamic decisions in the browser |
| Web/App Server | Back End | Hosts and delivers content for the web application |
| Database | Back End | Stores, modifies, and retrieves application data |
| Infrastructure | Back End | Networking, storage, and supporting systems/software |
| WAF (Web Application Firewall) | Back End (optional) | Filters incoming traffic to block malicious requests before they reach the server |

---

## 2. URL Anatomy

| Part | Example | What It Does |
|---|---|---|
| Scheme | `https://` | Protocol used to access the site (HTTP or HTTPS — HTTPS encrypts the connection) |
| User | `user:pass@site.com` | Optional login credentials embedded in the URL (rare, insecure) |
| Host/Domain | `tryhackme.com` | Identifies the specific website; watch for typosquatting (lookalike fake domains) |
| Port | `:443` | Directs the browser to the right service on the server (80 = HTTP, 443 = HTTPS) |
| Path | `/login` | Points to the specific file/page/resource on the server |
| Query String | `?search=term` | Passes extra key=value data to the server; needs sanitization to prevent injection |
| Fragment | `#section2` | Points to a specific section within the page; also needs sanitization |

---

## 3. HTTP Message Structure

Every HTTP request/response is made of four parts, in order:

| Part | What It Does |
|---|---|
| Start Line | Identifies the message type — request line (method/path/version) or status line (version/code/reason) |
| Headers | Key-value pairs giving extra instructions (content type, security, caching, etc.) |
| Empty Line | Separates headers from the body — required so the message parses correctly |
| Body | The actual data — form input/JSON in a request, page content/data in a response |

---

## 4. HTTP Request Methods

| Method | What It Does | Security Note |
|---|---|---|
| `GET` | Fetches data, no changes made | Don't put sensitive data (tokens/passwords) in GET — visible in plaintext/URLs |
| `POST` | Sends data to create/update something | Validate & sanitize input — prevents SQLi/XSS |
| `PUT` | Replaces/updates a resource | Verify authorization before accepting changes |
| `DELETE` | Removes a resource | Verify authorization before deleting |
| `PATCH` | Partially updates a resource | Validate data to avoid inconsistent state |
| `HEAD` | Like GET but headers only, no body | Useful for checking metadata cheaply |
| `OPTIONS` | Lists methods supported by a resource | Disable if unused — can leak server capability info |
| `TRACE` | Echoes back the request (debugging) | Usually disabled — security risk |
| `CONNECT` | Establishes a secure tunnel (e.g. HTTPS) | Critical for encrypted communication |

---

## 5. HTTP Versions

| Version | Year | Key Feature |
|---|---|---|
| HTTP/0.9 | 1991 | GET requests only |
| HTTP/1.0 | 1996 | Added headers, better content-type support/caching |
| HTTP/1.1 | 1997 | Persistent connections, chunked transfer encoding — still widely used |
| HTTP/2 | 2015 | Multiplexing, header compression, prioritization |
| HTTP/3 | 2022 | Built on HTTP/2, uses QUIC for faster/more secure connections |

---

## 6. Request Headers & Body Formats

### Common Request Headers

| Header | Example | What It Does |
|---|---|---|
| `Host` | `Host: tryhackme.com` | Specifies the target web server's domain |
| `User-Agent` | `User-Agent: Mozilla/5.0` | Identifies the browser/client making the request |
| `Referer` | `Referer: https://google.com/` | Indicates the page the request came from |
| `Cookie` | `Cookie: session=abc123` | Sends previously stored cookie data back to the server |
| `Content-Type` | `Content-Type: application/json` | Describes the format of the request body |

### Request Body Formats

| Format | Content-Type | What It Does |
|---|---|---|
| URL Encoded | `application/x-www-form-urlencoded` | `key=value` pairs joined by `&`; special chars percent-encoded |
| Form Data | `multipart/form-data` | Multiple data blocks separated by a boundary string; supports binary data (file uploads) |
| JSON | `application/json` | `"key": value` pairs in `{ }`, comma-separated |
| XML | `application/xml` | Data wrapped in nested opening/closing tags |

---

## 7. HTTP Response Structure

| Part | What It Does |
|---|---|
| Version | HTTP version used (e.g. HTTP/1.1) |
| Status Code | 3-digit number showing the outcome |
| Reason Phrase | Human-readable explanation of the status code |

### Status Code Categories

| Range | Category | Meaning |
|---|---|---|
| 100–199 | Informational | Request received, continue |
| 200–299 | Success | Request processed successfully |
| 300–399 | Redirection | Resource moved — check new location |
| 400–499 | Client Error | Problem with the request (bad URL, missing auth, etc.) |
| 500–599 | Server Error | Server-side failure, not the client's fault |

### Common Status Codes

| Code | Meaning |
|---|---|
| 100 | Continue |
| 200 | OK — request succeeded |
| 301 | Moved Permanently |
| 404 | Not Found |
| 500 | Internal Server Error |

---

## 8. Response Headers & Body

### Required/Common Response Headers

| Header | Example | What It Does |
|---|---|---|
| `Date` | `Date: Fri, 23 Aug 2024 10:43:21 GMT` | Timestamp of when the response was generated |
| `Content-Type` | `Content-Type: text/html; charset=utf-8` | Describes the content format and character set |
| `Server` | `Server: nginx` | Identifies the server software — often hidden to avoid leaking info to attackers |
| `Set-Cookie` | `Set-Cookie: sessionId=abc; HttpOnly; Secure` | Sends a cookie to store client-side; use `HttpOnly` (blocks JS access) and `Secure` (HTTPS only) |
| `Cache-Control` | `Cache-Control: max-age=600` | Controls how long the client can cache the response; `no-cache` prevents caching sensitive data |
| `Location` | `Location: /index.html` | Used in 3xx redirects to tell the client where to go — validate to prevent open-redirect attacks |

**Response Body:** the actual returned content (HTML, JSON, images, etc.) — always sanitize/escape user-generated content to prevent XSS.

---

## 9. Security Headers

| Header | Directive Example | What It Does |
|---|---|---|
| `Content-Security-Policy` (CSP) | `default-src 'self'; script-src 'self' https://cdn.example.com` | Restricts which domains scripts/styles/content can load from — mitigates XSS. `'self'` = same origin only |
| `Strict-Transport-Security` (HSTS) | `max-age=63072000; includeSubDomains; preload` | Forces HTTPS-only connections. `max-age` = duration (sec); `includeSubDomains` = applies to subdomains too; `preload` = browser enforces HTTPS before first visit |
| `X-Content-Type-Options` | `nosniff` | Stops the browser from guessing/sniffing MIME types — must rely on declared `Content-Type` |
| `Referrer-Policy` | `no-referrer` / `same-origin` / `strict-origin` / `strict-origin-when-cross-origin` | Controls how much referrer info is shared on navigation — from none, to same-origin only, to full path only when protocol/origin match |

**Tool:** [securityheaders.io](https://securityheaders.io/) — analyzes a site's security header configuration.

---

## 10. Quick Facts / Gotchas

- HTTPS ≠ optional in modern practice — HSTS forces it and prevents downgrade attacks.
- `GET` requests should never carry sensitive data — it's visible in browser history, logs, and the URL bar.
- Cookies should always use `HttpOnly` + `Secure` flags together for meaningful protection.
- CSP's `'self'` keyword refers to the same origin as the page serving the policy — not a wildcard.
- Disabling unused methods (`TRACE`, `OPTIONS` if not needed) reduces attack surface.
- Always validate/sanitize query strings, fragments, and body data — all are user-controllable and injection vectors.
