# Network Log Analysis 

---

## 1. Introduction to Network Log Analysis

### Core Concept
- Network devices form the backbone of internet infrastructure
- Modern networks use: hubs, switches, routers, firewalls, IPS/IDS, proxies, and WAFs
- **Goal**: Analyze logs from network devices to identify security incidents
- Logs vary by device/product; understanding the **essence** is key

---

## 2. Generic Log Analysis (NetFlow)

### What is NetFlow?
- Network protocol that collects IP traffic information
- Developed by Cisco; also supported by other vendors (Sflow, etc.)
- Provides **visibility** into network traffic patterns

### Key Benefits
| Benefit | Description |
|---------|-------------|
| **Billing & Accounting** | ISPs use for billing |
| **Network Design & Optimization** | Traffic analysis and capacity planning |
| **Network Monitoring** | Identifies frequently used ports and patterns |
| **QoS Measurement** | Monitors service quality and performance |
| **Security Analysis** | Detects anomalies and potential threats |

### How NetFlow Works
- **Stateful** monitoring of all IP traffic
- Each communication = **Flow** (set of packets sharing attributes)
- Exported in binary; interpreted by collector/analyzer tools

### Flow Attributes Collected
- Source & Destination IP Addresses
- Source & Destination Ports (TCP/UDP)
- IP Protocol
- Interface Information
- IP Version (IPv4/IPv6)

### What NetFlow Can Detect
- Abnormal traffic spikes
- Data leaks
- Unauthorized access to restricted systems
- New/unrecognized IPs or devices
- First-time connections to critical systems

---

## 3. Firewall Log Analysis

### What is a Firewall?
- Physical/virtual device controlling network traffic based on security policies
- **NGFW** (Next-Generation Firewall): Can inspect application-layer traffic (OSI Layer 7)
- NGFWs can identify specific applications regardless of port used

### Key Firewall Log Fields

| Field | Description |
|-------|-------------|
| `date/time` | Timestamp of event |
| `srcip/srcport` | Source IP and port |
| `dstip/dstport` | Destination IP and port |
| `action` | Action taken (accept/deny/drop) |
| `service` | Service/protocol used |
| `sentbyte/rcvdbyte` | Bytes sent/received |
| `duration` | Connection duration |

### Action Values
| Action | Meaning |
|--------|---------|
| **accept** | Packet passed successfully |
| **deny** | Blocked; notification sent to source |
| **drop** | Blocked; no notification sent |
| **close** | Mutual termination |
| **client-rst** | Terminated by client |
| **server-rst** | Terminated by server |

### Analysis Best Practices
1. Check IP and port information first
2. Examine the `action` field
3. Look for:
   - Suspicious IP/domain communication
   - Port scanning
   - Communication with IoCs
   - Lateral/vertical unauthorized access

### Investigation Tips
- Filter logs by source and destination IPs
- Check for repeated `accept` attempts from previously blocked IPs
- Correlate with other logs (AV, IPS, etc.)

---

## 4. VPN Log Analysis

### What is VPN?
- Technology for remote access to local networks
- Essential for secure remote access
- Represents a **prime attack surface**

### VPN Deployment Methods
- Integrated into firewalls (common)
- Standalone dedicated appliances
- Logs from either source

### Sample VPN Log Fields

| Field | Description |
|-------|-------------|
| `subtype` | "vpn" for VPN events |
| `remip` | Source IP initiating connection |
| `user` | Authenticated username |
| `reason` | Connection result (login successfully/failed) |
| `tunneltype` | SSL, IPSec, etc. |
| `tunnelip` | Internal IP assigned for VPN access |

### Critical Analysis Points
- Source IP of connection
- Authenticated username
- Access success/failure status

### Suspicious Activities to Detect
- Successful/unsuccessful VPN access attempts
- Brute-force attacks against VPN accounts
- VPN access from outside specified countries
- VPN access outside specified time periods

### Scenario Example
> After phishing email, check VPN logs for compromised users. Analyze successful access source IP, country info, and whether access appears legitimate.

---

## 5. Proxy Log Analysis

### What is a Proxy?
- Acts as bridge between endpoint and internet
- Used for: speed, centralized control, increased security

### Proxy Types
| Type | Description |
|------|-------------|
| **Transparent Proxy** | Target sees real source IP |
| **Anonymous Proxy** | Target sees proxy IP (not real source) |

### Popular Products
- Cisco Umbrella, Forcepoint Web Security Gateway, Check Point URL Filtering, Fortinet Secure Web Gateway

### Sample Proxy Log Fields

| Field | Description |
|-------|-------------|
| `srcip/srcport` | Source IP/port |
| `hostname` | Requested domain |
| `url` | Full URL requested |
| `action` | blocked/passed |
| `profile` | Applied policy profile |
| `urlsource` | Source of URL decision |
| `direction` | Traffic direction |

### Investigation Scenarios
- Check which domains/URLs a system requested
- Determine if connection was successful
- Identify if domain/URL is in risky category

### Suspicious Activities to Detect
- Connections to/from suspicious URLs
- Infected system detection
- Tunneling activities

### Analysis Tips
- Category numbers indicate domain classification
- Blocked requests from servers may indicate infection
- Correlate with EDR/XDR logs for process investigation

---

## 6. IDS/IPS Log Analysis

### Key Concepts
- **IDS**: Only detects suspicious activities
- **IPS**: Detects AND prevents suspicious activities
- **Signature Database**: Set of rules to detect known attacks
- Continuous updates against new attack vectors

### Popular Solutions
- Snort, Suricata (open source)
- Many firewall vendors offer as additional module

### Sample IPS Log Fields

| Field | Description |
|-------|-------------|
| `subtype` | "ips" for IPS events |
| `severity` | low/medium/high/critical |
| `srcip/dstip` | Source/destination IP |
| `attack` | Attack name/details |
| `attackid` | Attack ID |
| `action` | detected/blocked |
| `direction` | incoming/outgoing |

### Critical Analysis Points
1. **Attack direction**: Inbound or outbound?
2. **Severity level**: High/critical require quick action
3. **Multiple signatures**: More suspicious if different signatures triggered
4. **Port/service**: Does attack target match service on destination?
5. **Action taken**: Just detection or blocked?

### Suspicious Activities to Detect
- Port scanning
- Vulnerability scans
- Code injection attacks
- Brute-force attacks
- DoS/DDoS attacks
- Trojan/Botnet activities

### Investigation Tips
- Multiple requests from same source = raise severity
- If service doesn't match attack, likely false positive
- If blocked, monitor for repeated attempts
- If only detected, consider blocking action

---

## 7. WAF Log Analysis

### What is WAF?
- Web Application Firewall for securing web applications
- **SSL Offload**: Decrypts SSL traffic for inspection
- Without SSL offload, HTTPS payload cannot be inspected

### Popular Products
- F5 Big-IP, Citrix, Imperva, Forti WAF
- Cloud solutions: Cloudflare, Akamai, AWS WAF

### Sample WAF Log Fields

| Field | Description |
|-------|-------------|
| `main_type` | Detection type (Signature Detection) |
| `sub_type` | Attack detail (SQL Injection, XSS) |
| `severity_level` | Low/Medium/High/Critical |
| `src/src_port` | Source IP/port |
| `http_method` | GET/POST/PUT/DELETE |
| `http_url` | Requested URL |
| `http_host` | Requested host |
| `action` | Alert/Block |
| `attack_type` | SQL Injection, XSS, etc. |

### HTTP Methods
| Method | Purpose |
|--------|---------|
| **GET** | Retrieve data from server |
| **POST** | Send data to server (create) |
| **PUT** | Send data to server (create/update) |
| **DELETE** | Delete data on server |
| **OPTIONS** | Show which methods server accepts |
| **TRACE** | Message loop-back test |
| **HEAD** | Request metadata only |

### HTTP Response Codes
| Code | Meaning |
|------|---------|
| **200** | OK - Request successful |
| **301** | Permanent Redirect |
| **403** | Forbidden |
| **404** | Not Found |
| **503** | Service Unavailable |

#### Response Code Categories
- 100-199: Informational
- 200-299: Successful
- 300-399: Redirection
- 400-499: Client Errors
- 500-599: Server Errors

### Critical Analysis Points
- Attack type vs. application technology (e.g., PHP exploit on ASP = false positive)
- Response code indicates attack success:
  - `200` = attack likely successful
  - `404` = attack failed
  - `500` = server error (potential DoS)

### What to Detect
- SQL Injection, XSS, Code Injection, Directory Traversal
- Suspicious method usage (PUT, DELETE)
- Top requesting IPs
- Most requested URLs

---

## 8. Web Log Analysis

### What is Web Log Analysis?
- Critical due to web services being most common external-facing services
- Most common web servers: **Microsoft IIS**, **Apache**, **Nginx**
- Web logs are direct logs from web servers (not security products)

### Sample Web Log Format
```
71.16.45.142 - - [12/Dec/2021:09:24:42 +0200] "GET /?id=SELECT+*+FROM+users HTTP/1.1" 200 486 "-" "curl/7.72.0"
```


### Key Fields Analyzed

| Field | Description |
|-------|-------------|
| **Source IP** | Who made the request |
| **Date/Time** | When request occurred |
| **Request Method** | GET, POST, PUT, DELETE, etc. |
| **Requested URL** | Contains potential attack vectors |
| **HTTP Version** | HTTP/1.1, etc. |
| **Server Response** | Status code (200, 404, etc.) |
| **Data Size** | Size of returned data |
| **User-Agent** | Browser/tool used |

### Attack Detection Examples

| Attack Type | URL Pattern Example |
|-------------|---------------------|
| SQL Injection | `?id=SELECT+*+FROM+users` |
| XSS | `?q=<script>alert(1)</script>` |
| Directory Traversal | `?file=../../etc/passwd` |
| Code Injection | `?page=<?php system($_GET['cmd']); ?>` |

### Analysis Flow for Web Attacks
1. **Identify attack vector** in URL
2. **Check response code**:
   - `200` = Attack may have succeeded
   - `404` = Attack failed
   - `500` = Potential DoS
3. **Check User-Agent**:
   - Browser agents = likely real user
   - Scanner tools (nikto, nessus, nmap) = automated attacks
   - **Caution**: User-Agent can be spoofed

### Example Analysis Process

**Raw Request:**
```
192.168.8.54 - - [29/Jun/2022:07:42:48 +0300] "GET /bwapp/sqli_1.php?title=%25iron%27+union+select+1%2Cuser()%2C3%2C4%2C5%2C6%2C7--+-+%25%27&action=search HTTP/1.1" 200 13539 "..." "Mozilla/5.0..."
```

Decoded as : /bwapp/sqli_1.php?title=%iron' union select 1,user(),3,4,5,6,7-- - %'&action=search


**Analysis:**
- SQL Injection attack detected (`union select`, `user()`)
- Response 200 = Attack **SUCCESSFUL**
- Source IP: 192.168.8.54
- User-Agent: Genuine browser (but could be spoofed)

### What Web Logs Help Detect
- Web requests with attack vectors
- Top requesting IP addresses
- Most requested URLs
- Most received HTTP response codes
- Suspicious method usage (PUT, DELETE)

---

## 9. DNS Log Analysis

### What is DNS?
- Basic building block of internet
- Domain → IP address resolution
- Every network traffic uses IPs; DNS provides the mapping

### Why SOC Analysts Use DNS Logs
- Check which domains were requested
- Identify time of domain requests
- Detect unauthorized domain accesses

### Two Categories of DNS Logs

#### 1. DNS Server Records
- Audit events on DNS server
- Track: adding, deleting, editing records
- **Windows**: Eventlog → Application and Services Logs → Microsoft → Windows → DNS-Server\Audit
- **Example**: Event_ID: 516 (Record Deletion)

#### 2. DNS Queries
- Records of systems querying domains
- Not enabled by default; must be configured
- Sources: Microsoft DNS, Bind DNS, Dnsmasq

### Sample DNS Query Log
```
{ "timestampt": 1591367999.306059, "source_ip": "192.168.4.76", "source_port": 36844, "destination_ip": "192.168.4.1", "destination_port": 53, "protocol": "udp", "query": "testmyids.com", "qtype_name": "A" }
```


### Key DNS Query Fields
| Field | Description |
|-------|-------------|
| Date-Time | When query was made |
| Querying IP/Port | Source of request |
| Query Type | A, AAAA, MX, etc. |
| Requested Domain | Domain being resolved |

### Suspicious DNS Activities

| Activity | Suspicion |
|----------|-----------|
| Random subdomains | DNS tunneling |
| First-time visited domains | Unknown communication |
| Domain > certain char length | Potential data exfiltration |
| NX returning domains | Domain generation algorithm (DGA) |
| Domain IoC controls | Malicious domain communication |
| DoT/DoH access | Bypassing DNS security controls |

### Example Analysis

**Suspicious Activity:**
```
192.168.10.12 → multiple random subdomain queries in 1 minute → Potential DNS Tunneling
```


**Potentially Malicious:**
```
Feb 5 09:12:11 ns1 named[80090]: client 192.168.10.3#3261: query: login.microsoftonline.com IN A
Feb 5 09:13:11 ns1 named[80090]: client 192.168.10.3#4536: query: onedrive.live.com IN A
```

> Oracle Database server querying Microsoft services = **Suspicious data exfiltration activity**

### Investigation Tips
- Check if system should access certain domains
- Verify domain reputation/category
- Use Threat Intelligence for IoC matching
- Correlate with endpoint logs for process identification

---

## Quick Reference: Log Source Comparison

| Log Source | Purpose | Key Fields to Check | Common Suspicious Activities |
|------------|---------|---------------------|------------------------------|
| **NetFlow** | Traffic visibility | IPs, Ports, Bytes | Spikes, data leaks, new IPs |
| **Firewall** | Access control | Action, IPs, Ports | Port scanning, IoC comms, lateral movement |
| **VPN** | Remote access | remip, user, reason | Brute force, unauthorized access, geo anomalies |
| **Proxy** | Internet control | URL, action, hostname | Suspicious URLs, tunneling, infected systems |
| **IDS/IPS** | Threat detection | attack, severity, action | Scans, exploits, botnet, trojan |
| **WAF** | Web protection | http_url, sub_type, response | SQLi, XSS, code injection, PUT/DELETE |
| **Web Logs** | Server requests | URL, response code, User-Agent | Attack vectors, response status, scanning tools |
| **DNS** | Resolution | domain, source_ip, query type | Tunneling, DGA, IoC, exfiltration |

---

## Key Takeaways

1. **Firewalls are first line of defense** - Check action field first
2. **VPN logs are critical for remote access security** - Monitor for brute force
3. **Proxies show all outbound web requests** - Detect C2 communication
4. **IDS/IPS detects network attacks** - Severity and multiple signatures matter
5. **WAF protects web applications** - Look for attack type and response code
6. **Web logs are from servers, not security devices** - Direct evidence of attacks
7. **DNS logs reveal what systems are accessing** - First-time domains are suspicious
8. **Always correlate multiple log sources** - Single source is rarely conclusive
9. **Understand the log source location in the network** - Context matters
10. **User-Agent and Request Methods can be spoofed** - Never trust alone
11. **Response codes indicate attack success/failure** - 200 = likely successful
12. **IoC checking across all logs is essential** - IPs, domains, hashes

---


























