# Networkwalks CyberSecurity Internship - Week 2
#  Footprinting & Network Scanning
## Module 1 — Footprinting & Reconnaissance

## Objective
Perform footprinting and reconnaissance against a lab target using tools available in Kali Linux.

**Target domain:** `networkwalks.com`

**Tools used:** WHOIS · WhatWeb · NSLookup · cURL · WAFW00F · DNSRecon

> ⚠️ All activity was performed for educational purposes against a lab-designated target.

---

## Task 1 — WHOIS

**Command**
```bash
whois networkwalks.com
```

**Purpose:** Retrieve public domain registration details such as registrar, dates and name servers.

**Key Results**

| Field | Value |
|---|---|
| Domain | NETWORKWALKS.COM |
| Registrar | GoDaddy.com, LLC |
| Creation Date | 2019-11-06 |
| Expiry Date | 2027-11-06 |
| Name Servers | NS6135.HOSTGATOR.COM, NS6136.HOSTGATOR.COM |
| DNSSEC | Unsigned |

![WHOIS](module1screenshot/whois.png)

---

## Task 2 — WhatWeb

**Command**
```bash
whatweb networkwalks.com
```

**Purpose:** Identify technologies and services used by the target website.

**Observed Information**

| Item | Value |
|---|---|
| Web Server | Apache |
| IP Address | 192.232.216.135 |
| CMS | WordPress |
| WordPress Download Manager | 3.3.76 |
| jQuery | 3.7.1 |
| Bootstrap | 7.1.4 (as reported by WhatWeb) |
| Other | Google Tag Manager, HTML5 |
| Title | Networkwalks Academy |
| Redirect | HTTP → HTTPS (301) |

![WhatWeb](module1screenshot/whatweb.png)

---

## Task 3 — NSLookup

**Command**
```bash
nslookup networkwalks.com
```

**Purpose:** Resolve the domain name to its IP address.

**Results**

| Item | Value |
|---|---|
| DNS Server | 8.8.8.8 |
| Resolved IP | 192.232.216.135 |

![NSLookup](module1screenshot/nslookup.png)

---

## Task 4 — HTTP Response Headers

**Command**
```bash
curl -I networkwalks.com
```

**Purpose:** Inspect the HTTP response headers returned by the target.

**Observed Information**
- Status: `HTTP/1.1 301 Moved Permanently`, redirecting to `https://networkwalks.com/`
- `Server: Apache`
- `X-Redirect-By: WordPress - Really Simple Security`
- `X-nginx-cache: WordPress`
- `Content-Type: text/html; charset=UTF-8`
- Cookie set: `__wpdm_client` (HttpOnly)
- `Referrer-Policy: no-referrer-when-downgrade`
- `Permissions-Policy` present
- Cache headers: `Cache-Control: max-age=3600`, `Expires`, `X-Endurance-Cache-Level`

![cURL Headers](module1screenshot/curl.png)

---

## Task 5 — Web Application Firewall Detection

**Command**
```bash
wafw00f networkwalks.com
```

**Purpose:** Determine whether a WAF protects the target.

**Result:** The site `https://networkwalks.com` is behind **ModSecurity (SpiderLabs)** WAF.

![WAFW00F](module1screenshot/wafw00f.png)

---

## Task 6 — DNS Enumeration

**Command**
```bash
dnsrecon -d networkwalks.com
```

**Purpose:** Enumerate publicly available DNS information.

**Records Observed**

| Type | Value |
|---|---|
| NS | ns6135.hostgator.com (50.87.144.87), ns6136.hostgator.com (192.232.216.131) |
| MX | mail.networkwalks.com (192.232.216.135) |
| A | networkwalks.com → 192.232.216.135 |
| TXT | Google site verification; SPF record (`v=spf1 +a +mx ...`) |
| SRV | `_autodiscover._tcp` → cpanelemaildiscovery.cpanel.net (8 records) |

**Notes:** The SOA lookup timed out and no DNSSEC answer was returned. The tool reported **8 Records Found** in the SRV enumeration.

![DNSRecon](module1screenshot/dnsrecon.png)

---

## Skills Practiced
- Domain footprinting
- Web technology fingerprinting
- DNS enumeration
- HTTP header analysis
- WAF detection
- IP address identification
- Reconnaissance with Kali Linux command-line tools

# Module 3 — Footprinting with Maltego

## Objective
Install Maltego on a Windows computer and use it to perform OSINT footprinting by discovering email addresses associated with a target domain.

**Target domain:** `networkwalks.com` (lab-designated target, used with due permission)

**Tool used:** Maltego Graph (Desktop) 4.14.0 — Community Edition
---

## Task 1 — Download & Install Maltego on Windows

**Purpose:** Set up Maltego, a graphical OSINT and link-analysis tool, on a Windows machine.

**Steps performed**
1. Downloaded Maltego Graph for Windows from the official website (https://maltego.com).
2. Ran the setup as administrator and completed the installer (including the Java Runtime prerequisite).
3. Launched Maltego and chose **Maltego ID** activation (online).
4. Created a free Maltego ID and logged in through the browser.
5. Completed the configuration wizard with the default settings (Utilities transforms, default system browser, Privacy Mode: Normal).
6. Opened a blank graph, confirming Maltego was ready to run transforms.

**Result**

| Item | Value |
|---|---|
| Operating System | Windows |
| Application | Maltego Graph (Desktop) |
| Version | 4.14.0 |
| Activation | Maltego ID (free account) |
| Privacy Mode | Normal |
| Status | Installed and ready to run transforms |

![Maltego installed](module3screenshot/maltegoinstalled.png)

---

## Task 2 — Find Email Addresses Related to the Target Domain

**Purpose:** Use a Maltego transform to harvest publicly available email addresses linked to `networkwalks.com`.

**Steps performed**
1. Searched for **Domain** in the Entity Palette and dragged it onto the graph.
2. Double-clicked the entity and renamed it to `networkwalks.com`.
3. Right-clicked the entity, filtered for `email`, and ran the transform:
   `[Utilities] To Emails @domain [Search Engine]`

**Transform output**
- The transform ran on 1 entity (`networkwalks.com`).
- It completed in about 16 seconds and returned 1 entity.
- It consumed 10 credits (190 remaining on the free account).

**Result**

| Entity Type | Value |
|---|---|
| Domain | networkwalks.com |
| Email Address | info@networkwalks.com |

The graph shows 2 entities and 1 link: the domain connected to the discovered email address.

![Domain to email transform](module3screenshot/domainemailtransform.png)

---

## Why This Matters
Email addresses discovered during footprinting help an attacker identify contacts for phishing or social engineering, and combined with other reconnaissance (such as the WordPress fingerprint found in Module 1) they help select technology-specific attacks. Defenders can use the same technique to see what an organization exposes publicly.

## Notes
- Maltego has many other transforms (DNS lookups, WHOIS details, person-from-domain, and more) that can be explored for further footprinting.
- Maltego graphs can be exported to CSV, XLSX, image and PDF formats.

## Skills Practiced
- OSINT and passive footprinting
- Installing and configuring security tools on Windows
- Link analysis and graph-based investigation
- Email address enumeration
- Using transforms and understanding data-source credit limits

# Module 4 — Footprinting & Reconnaissance with theHarvester

## Objective
Use theHarvester in Kali Linux to gather email addresses, sub-domains and hosts for a target organization from public sources, without touching the target directly (passive reconnaissance).

**Target domain:** `microsoft.com`

**Tool used:** theHarvester 4.11.1 (pre-installed in Kali Linux)
---

## Background
theHarvester is a Python-based OSINT tool that collects emails, sub-domains, hosts, employee names, open ports and banners from public sources such as search engines, PGP key servers and Shodan.

| Option | Meaning |
|---|---|
| `-d` | Target domain |
| `-l` | Limit the number of results |
| `-b` | Data source (e.g. `baidu`, `all`) |

---

## Task 1 — Emails & Sub-domains using Baidu (limit 1000)

**Command**
```bash
theHarvester -d microsoft.com -l 1000 -b baidu
```

**Purpose:** Find email IDs and sub-domains related to `microsoft.com` using Baidu as the only source, with the result limit set to 1000.

**Results**

| Item | Result |
|---|---|
| Target | microsoft.com |
| Source | Baidu |
| IPs found | None |
| Emails found | None |
| People found | None |
| Hosts / sub-domains found | None |

**Observation:** Baidu returned no data for this domain at the time of the scan. theHarvester relies on third-party sources whose results change over time, so a single source can come back empty.

![Task 1 – Baidu](module4screenshot/task1.png)

---

## Task 2 — Emails & Sub-domains using All Sources (limit 50)

**Command**
```bash
theHarvester -d microsoft.com -l 50 -b all
```

**Purpose:** Query all supported sources at once, with the result limit set to 50.

**Observations**
- theHarvester 4.11.1 started the search against `microsoft.com` and read `/etc/theHarvester/api-keys.yaml` for each source.
- Several sources failed with a **Missing API key** error because no API keys were configured:

| Source | Status |
|---|---|
| BeVigil | Missing API key |
| Bitbucket | Missing API key |
| BufferOverrun | Missing API key |
| BuiltWith | Missing API key |
| Brave Search | Missing API key |
| Censys | Missing API key (ID and/or Secret) |
| CriminalIP | Missing API key |
| Dehashed | Missing API key |
| DNSDumpster | Missing API key |

Sources that need an API key return no data until a key is added to `api-keys.yaml`.

![Task 2 – all sources](module4screenshot/task2.png)

---

## Why This Matters
theHarvester only reads public data, so the target never knows it is being studied. Attackers can use harvested emails for phishing and password attacks, and sub-domains widen the attack surface. Defenders run the same tool on their own organization to see what an attacker would see and reduce what they leak.

## Skills Practiced
- Passive reconnaissance and OSINT
- Using theHarvester with different data sources
- Understanding result limits (`-l`) and source selection (`-b`)
- Troubleshooting missing API key errors
- Documenting tool output as evidence

# Module 5 — Network Scanning with Zenmap

## Objective
Use Zenmap, the official GUI for Nmap, to discover live hosts on a local network, identify their IP and MAC addresses, and visualize the network topology.

**Tool used:** Zenmap / Nmap 7.993 on Windows
---

## Task 1 — Download & Install Zenmap on Windows

**Purpose:** Install Nmap with its Zenmap GUI from the official website (https://nmap.org/download.html).

**Result:** Zenmap is installed and running on Windows. The desktop shows the **Nmap - Zenmap GUI** shortcut, and Zenmap opens with the default *Intense scan* profile (`nmap -T4 -A -v`).

![Zenmap installed](module5screenshot/zenmapinstalled.png)

---

## Task 2 — Find Local IP Address & LAN Subnet

**Command**
```cmd
ipconfig
```

**Purpose:** Identify the PC's local IP address and subnet.

**Results (Wi-Fi adapter)**

| Item | Value |
|---|---|
| IPv4 Address | 192.168.1.37 |
| Subnet Mask | 255.255.255.0 (/24) |
| Default Gateway | 192.168.1.1 |
| DNS Suffix | bbrouter |
| LAN Subnet | 192.168.1.0/24 |

The Ethernet 2 adapter (`192.168.56.1/24`) is a virtual adapter, likely from VirtualBox. It has no default gateway, so it is not the LAN used for the scan.

![ipconfig](module5screenshot/ipconfig.png)

---

## Task 3 — Find Live Hosts in the Subnet

**Command**
```bash
nmap -sn 192.168.1.0/24
```

**Purpose:** Run a Ping scan (host discovery only, no port scan) in Zenmap against the LAN subnet found in Task 2.

**Result:** Nmap scanned 256 IP addresses and found 4 hosts up in 12.25 seconds.

![Ping scan output](module5screenshot/pingscanoutput.png)

---

## Task 4 — How many hosts are live?

**Answer:** **4 hosts** are live, including my own PC.

---

## Task 5 — IP addresses of the live hosts

| # | IP Address | Notes |
|---|---|---|
| 1 | 192.168.1.1 | Default gateway (router), latency 0.18s |
| 2 | 192.168.1.35 | Latency 0.41s |
| 3 | 192.168.1.38 | Latency 0.17s |
| 4 | 192.168.1.37 | My own PC (confirmed by `ipconfig`) |

---

## Task 6 — MAC addresses of the live hosts

| IP Address | MAC Address | Vendor (per Nmap) |
|---|---|---|
| 192.168.1.1 | - | GX International BV |
| 192.168.1.35 | - | Unknown |
| 192.168.1.38 | - | Unknown |
| 192.168.1.37 | Not shown (local host) | Use `ipconfig /all` to find it |

Mac addresses are not shown for privacy concern.

---

## Task 7 — Network Topology

**Purpose:** View the scan results as a topology graph and save it as a PDF.

**Steps**
1. Opened the **Topology** tab in Zenmap.
2. Turned on the **Legend**.
3. Clicked **Save Graphic** and chose **PDF**.

**Result:** The topology shows `localhost` at the center, connected to all four live hosts (`192.168.1.1`, `.35`, `.37`, `.38`). All are green, meaning each host had fewer than 3 open ports found.

![Topology](module5screenshot/topology.png)
---

## Skills Practiced
- Network discovery and host identification
- IP and MAC address identification
- Ping scanning with Nmap / Zenmap
- Network topology visualization
- Reading `ipconfig` output to find a LAN subnet

## Final Report (W2-PM-FINAL)

The detailed penetration-testing report covering the modules above is available here:

> 🔗 **Report link:** https://docs.google.com/document/d/1GtPMuU0eG4y8hwnVNV51CPtuk9gZnDdu/edit?usp=sharing&ouid=111247344041454589335&rtpof=true&sd=true