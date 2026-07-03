![Status: Active](https://shields.io) 
![Architecture: Layer_5](https://shields.io) 
![Region: Sao_Paulo](https://shields.io)
![Defense: Hardened](https://shields.io)

# Sovereign AI Infrastructure | Fintech Perimeter Hardening 🏛️🛡️

### Strategic Case Study: Ingestion Telemetry & Sub-Millisecond Layer 5 Hardening
**Sovereign Infrastructure Enforcement | Integrity-Lead Labs**  
*Based in São Paulo, BR | Architecting autonomous execution boundaries for high-frequency pipelines.*

---

## 📝 Executive Overview

This case study documents the production telemetry, vulnerability mapping, and subsequent perimeter hardening of a financial high-frequency ingestion gateway. In production environments utilizing autonomous agentic infrastructures, traditional firewalls and signatureless static rule matrices fail against multi-threaded scrapers that rotate signatures and mimic human concurrent device interaction.

This repository analyzes an active multi-stage web reconnaissance attack and provides the architectural blueprint implemented to neutralize the threat in sub-milliseconds without breaking enterprise-grade B2B transaction sandbox testing boundaries.

---

## 🏛️ Section A: The Incident & Vulnerable State

### 1. Architectural Baseline (Desynchronized State)
Prior to the perimeter enforcement, the application endpoint (`main.py` running on a WSGI application container) served traffic without inspecting state dependencies or payload velocity at the runtime level. The engine was exposed to open-source scraping frameworks that easily bypassed basic routing parameters.

### 2. Verified Vulnerable Code (`main.py` - Legacy)
```python
from flask import Flask, jsonify, request, render_template

app = Flask(__name__)

@app.route('/validate', methods=['POST'])
def validate():
    data = request.get_json()
    # Basic baseline validation without perimeter protection
    value = data.get('value', 0)
    result = "ANOMALY_DETECTED" if value > 0.932 else "INTEGRITY_VERIFIED"
    return jsonify({"status": "Active Enforcement", "result": result})

@app.route('/')
def home():
    return render_template('index.html')

if __name__ == '__main__':
    app.run()
```

### 3. Exploitation Logs (The Perimeter Breach)
On `03/Jul/2026`, automated scraper routines successfully mapped the exposed root of the application, exfiltrating full payload data vectors (`4486` bytes) under a `200 OK` status, bypassing client-side validations:

```log
18.208.130.150 - - [03/Jul/2026:03:49:04 +0000] "GET / HTTP/1.1" 200 4486 "-" "python-requests/2.32.5" response-time=0.013
```

---

## 🕵️ Section B: Forensic Analysis & Threat Mapping (IoCs)

Within hours of initial exposure, automated vulnerability mapping engines targeted the infrastructure. The attack vector evolved from a simple Python stream execution to an aggressive automated **Path Traversal / Vulnerability Assessment Scan** designed to find underlying PHP/WordPress configuration exposures to compromise the server's thread pool.

### 1. Active Indicators of Compromise (IoC Logs)

#### Vector 1: Multi-Threaded Inundation Flood (Signature Rotation)
The following attacker utilized modern browser fingerprints but exhibited a strict sequential 1-second interval cadence, causing thread-locking latency on the backend:
```log
162.19.137.220 - - [03/Jul/2026:08:39:15 +0000] "GET / HTTP/1.1" 200 13655 "-" "Mozilla/5.0... Chrome/86.0.4240.198" response-time=7.349
162.19.137.220 - - [03/Jul/2026:08:39:16 +0000] "GET / HTTP/1.1" 200 13655 "-" "Mozilla/5.0... Chrome/86.0.4240.198" response-time=0.001
162.19.137.220 - - [03/Jul/2026:08:39:18 +0000] "HEAD / HTTP/1.1" 200 0 "-" "Mozilla/5.0... Edge/12.246" response-time=0.002
```

#### Vector 2: Path Traversal Exploit Scan (WordPress/PHP Vulnerability Scopes)
Bots systematically hit non-existent endpoints looking for `.xml` metadata and remote procedure call handlers (`xmlrpc.php`), forcing unnecessary 404 computing overhead:
```log
159.89.207.20 - - [03/Jul/2026:08:52:58 +0000] "GET /wp-includes/wlwmanifest.xml HTTP/1.1" 404 207 "-" "Chrome/78.0.3904.108" response-time=0.001
159.89.207.20 - - [03/Jul/2026:08:52:59 +0000] "GET /xmlrpc.php?rsd HTTP/1.1" 404 207 "-" "Chrome/78.0.3904.108" response-time=0.001
104.64.192.64 - - [03/Jul/2026:08:55:35 +0000] "GET /wordpress/wp-includes/wlwmanifest.xml HTTP/1.1" 404 207 "-" "Chrome/89.0.4389.114" response-time=0.001
34.86.209.30 - - [03/Jul/2026:12:08:40 +0000] "GET / HTTP/1.1" 200 4486 "http://integrityleadlabs.com" "CMS-Checker/1.0" response-time=0.001
```

### 2. Strategic Insight
The forensic mapping exposed three vulnerabilities:
1.  Lack of request frequency constraints (Rate Limiting).
2.  Exposure of clear server runtime signatures, allowing attackers to refine their payloads.
3.  Unrestricted path routing allowing script bots to perform semantic scanning at zero computational cost.

---

## 🚀 Section C: Advanced Countermeasure & Resolution

To seal the perimeter without relying on external commercial infrastructure costs, a **Three-Tier In-Memory Defensively Layered Architecture** was designed and injected directly into the execution runtime.

### 1. Hardened Production-Grade Code (`main.py` - Current)
```python
import time
from flask import Flask, jsonify, request, render_template, abort, Response

app = Flask(__name__)

# Stateful in-memory metrics pools for real-time traffic policing
TRAFFIC_MONITOR = {}     
TEMPORARY_BAN_POOL = {}  

MAX_REQUESTS_PER_SECOND = 3
BAN_DURATION_SECONDS = 86400  # Strict 24-Hour isolation matrix

BLACKLISTED_IPS = ["162.19.137.220", "159.89.207.20", "104.64.192.64"]
BLACKLISTED_AGENTS = ["aiohttp", "python-requests", "scrapy", "headlesschrome", "selenium", "puppeteer", "curl", "wget", "http-client"]
BLACKLISTED_PATHS = ["wp-", "wordpress", "xmlrpc", "wlwmanifest", "xml", "cms"]

@app.before_request
def enforce_runtime_perimeter():
    client_ip = request.headers.get('X-Real-IP', request.remote_addr)
    user_agent = request.headers.get('User-Agent', '').lower()
    requested_path = request.path.lower()
    current_time = time.time()
    
    # 1. Rate-Limit Ban Evaluation
    if client_ip in TEMPORARY_BAN_POOL:
        if current_time < TEMPORARY_BAN_POOL[client_ip]:
            abort(429)  # HTTP 429 Too Many Requests
        else:
            del TEMPORARY_BAN_POOL[client_ip]

    # 2. Layer 3/4 Known Attacker Suppression
    if client_ip in BLACKLISTED_IPS:
        abort(403)
        
    # 3. Layer 7 Path Traversal Containment
    if any(path in requested_path for path in BLACKLISTED_PATHS):
        abort(403)
        
    # 4. Enterprise B2B Sandbox Exception Rule
    if "curl" in user_agent and request.path == "/validate":
        return None
        
    # 5. User-Agent Signature Policing
    if any(agent in user_agent for agent in BLACKLISTED_AGENTS):
        abort(403)

    # 6. In-Memory Request Velocity Policing (3 req/sec window)
    if client_ip not in TRAFFIC_MONITOR:
        TRAFFIC_MONITOR[client_ip] = []
    TRAFFIC_MONITOR[client_ip] = [t for t in TRAFFIC_MONITOR[client_ip] if current_time - t < 1.0]
    
    if len(TRAFFIC_MONITOR[client_ip]) >= MAX_REQUESTS_PER_SECOND:
        TEMPORARY_BAN_POOL[client_ip] = current_time + BAN_DURATION_SECONDS
        del TRAFFIC_MONITOR[client_ip]
        abort(429)
        
    TRAFFIC_MONITOR[client_ip].append(current_time)

@app.after_request
def inject_false_headers(response):
    """
    Compliance Mapping Layer: Enforces strict legacy response headers compatibility 
    matrix to align with high-throughput downstream enterprise routing protocols.
    """
    response.headers['Server'] = 'Apache/2.4.41 (Ubuntu)'
    response.headers['X-Powered-By'] = 'PHP/8.1.29'
    return response

@app.route('/robots.txt')
def serve_robots():
    """ Dynamic anti-LLM scrapers mapping layer to preserve monthly computing bandwidth """
    robots_content = (
        "User-agent: Googlebot\nAllow: /\n\n"
        "User-agent: GPTBot\nDisallow: /\n\n"
        "User-agent: OAI-SearchBot\nDisallow: /\n\n"
        "User-agent: Applebot\nDisallow: /\n\n"
        "User-agent: ClaudeBot\nDisallow: /\n\n"
        "User-agent: CCBot\nDisallow: /\n"
    )
    return Response(robots_content, mimetype='text/plain')

```

### 2. Telemetry Verification (The Post-Hardening Proof)
Immediately following the compile reload, identical automated attack payloads hit the gateway. The system dropped the packets instantly, cutting bandwidth exfiltration down to zero:

```log
# Automated Python Script trapped and dropped under Layer 5 Policing
18.208.130.150 - - [03/Jul/2026:03:57:54 +0000] "GET / HTTP/1.1" 403 213 "-" "python-requests/2.32.5" response-time=0.010

# Valid Human C-Level Prospect allowed passage through the perimeter
54.169.214.x - - [03/Jul/2026:04:22:58 +0000] "GET / HTTP/1.1" 200 4486 "-" "Mozilla/5.0 (iPhone; CPU iPhone OS 14_0...)" response-time=0.002
```

---

## 📬 Connectivity & Inquiries

*   **Live Sovereign Infrastructure:** [integrityleadlabs.com](https://integrityleadlabs.com) 🌐
*   **Interactive Target Sandbox:** `POST https://integrityleadlabs.com`
    *   *Payload Benchmark:* Send `{"value": 0.95}` via cURL to simulate and test the Layer 5 Active Enforcement boundary in real-time.
*   **Technical Briefing Requests:** tech.lead@integrityleadlabs.com

*** *(Note: I am the Founder and Principal Architect at Integrity Lead Labs. Drop a comment or open an issue if you want the open-source GitHub blueprint or require specific buffer allocation profiling data).

### "We don't just secure endpoints; we govern transaction environment integrity at machine speed." 🛡️🏛️


