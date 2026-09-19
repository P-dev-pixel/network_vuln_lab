# Network Vulnerability Scanner

A lightweight Python-based network scanning tool that identifies open TCP ports, gathers basic service and banner information, and searches Exploit-DB for potentially relevant known exploits based on detected service and version information.

This project was built to understand practical concepts in **network scanning, service enumeration, banner grabbing, vulnerability research, and security testing**.

> **Disclaimer:** This tool is intended for educational purposes and authorized security testing only. Scan only systems and networks that you own or have explicit permission to test.

---

## Features

* 🔍 **TCP Port Scanning**

  * Uses Nmap to scan a target for open TCP ports.
  * Supports custom port ranges.
  * Default range: `1-1024`.

* 🧩 **Service & Version Detection**

  * Retrieves service names and version information from Nmap.
  * Helps identify software running on exposed ports.

* 📡 **Banner Grabbing**

  * Uses Python sockets to connect to discovered ports.
  * Sends a basic HTTP `HEAD` request and attempts to retrieve a service response.

* 🛡️ **Exploit-DB Lookup**

  * Searches Exploit-DB using the detected service name and version.
  * Reports potentially relevant exploit entries.

* 📄 **JSON Report Generation**

  * Saves scan results to a JSON file.
  * Includes target information, open ports, service details, banners, and potential exploit references.

* 💻 **Command-Line Interface**

  * Supports target IP/hostname and custom port ranges through command-line arguments.

---

## How It Works

The scanner follows a simple workflow:

```text
Target IP / Hostname
        │
        ▼
   Nmap Port Scan
        │
        ▼
Identify Open TCP Ports
        │
        ▼
Service & Version Detection
        │
        ▼
    Banner Grabbing
        │
        ▼
 Exploit-DB Search
        │
        ▼
 Potential Exploit References
        │
        ▼
    JSON Report
```

---

## Technologies Used

| Technology    | Purpose                            |
| ------------- | ---------------------------------- |
| Python        | Core programming language          |
| Nmap          | Port and service scanning          |
| `python-nmap` | Python interface for Nmap          |
| Requests      | HTTP requests to Exploit-DB        |
| BeautifulSoup | HTML parsing                       |
| Socket        | TCP connection and banner grabbing |
| Argparse      | Command-line argument handling     |
| JSON          | Scan result storage                |

---

## Project Structure

```text
network-vulnerability-scanner/
│
├── scanner.py
├── requirements.txt
├── README.md
└── scan_results.json
```

> The generated JSON filename is based on the target, for example:
> `<target>_scan_results.json`

---

## Requirements

Before running the project, install:

### 1. Python

Python 3.9+ is recommended.

Check your installation:

```bash
python --version
```

### 2. Nmap

Nmap must be installed separately because the Python library communicates with the Nmap executable.

Verify the installation:

```bash
nmap --version
```

### 3. Python Dependencies

Install the required packages:

```bash
pip install python-nmap requests beautifulsoup4
```

Or use the project's requirements file:

```bash
pip install -r requirements.txt
```

Example `requirements.txt`:

```text
python-nmap
requests
beautifulsoup4
```

---

## Usage

Run the scanner with a target IP address or hostname:

```bash
python scanner.py <target>
```

Example:

```bash
python scanner.py 192.168.1.10
```

The default port range is:

```text
1-1024
```

### Scan a Custom Port Range

```bash
python scanner.py 192.168.1.10 -p 1-100
```

Or:

```bash
python scanner.py 192.168.1.10 --ports 80,443,8080
```

---

## Example Output

```text
[*] Starting scan on 192.168.1.10...
[*] Scanning 192.168.1.10 for open ports...

[*] Open ports on 192.168.1.10: 22, 80

[*] Grabbing service banners and checking vulnerabilities...

  Port 22 - SSH-2.0-OpenSSH...
  Port 80 - HTTP/1.1 200 OK...

[*] Checking vulnerabilities for ssh OpenSSH ... on port 22...
[*] Checking vulnerabilities for http Apache ... on port 80...

Scan Results:

Open Ports for 192.168.1.10:
  Port 22: ssh OpenSSH ... - SSH-2.0-OpenSSH...
  Port 80: http Apache ... - HTTP/1.1 200 OK...

Vulnerabilities found:
  Port 80:
    Example exploit entry

[*] Results saved to 192.168.1.10_scan_results.json
```

The exact output depends on the target system and the service information returned by Nmap.

---

## JSON Output

The scanner stores the results in a JSON file.

Example structure:

```json
{
    "target": "192.168.1.10",
    "open_ports": {
        "80": {
            "name": "http",
            "product": "Apache",
            "version": "2.x",
            "banner": "HTTP/1.1 200 OK"
        }
    },
    "vulnerabilities": {
        "80": [
            "Example exploit reference"
        ]
    }
}
```

---

## Main Components

### Port Scanning

The `port_scan()` function uses Nmap to identify open ports and collect service information.

```python
def port_scan(target, ports):
    nm = nmap.PortScanner()
    nm.scan(target, ports)
    return nm[target]
```

### Banner Grabbing

The `banner_grab()` function establishes a TCP connection and attempts to retrieve information returned by the service.

```python
s.connect((target, port))
s.send(b'HEAD / HTTP/1.0\r\n\r\n')
```

This provides additional information that can sometimes help identify the running service.

### Exploit-DB Search

The scanner constructs a search query using the detected service and version:

```text
https://www.exploit-db.com/search?q=<service>+<version>
```

The returned HTML is parsed with BeautifulSoup to extract exploit names.

### Result Storage

The final scan information is saved as JSON so it can be reviewed later or used for further analysis.

---

## Security Considerations

This project is intentionally simple and is designed for learning rather than production-grade vulnerability assessment.

The Exploit-DB lookup **does not prove that a target is vulnerable**. A matching exploit entry may require specific software versions, configurations, operating systems, or conditions that have not been verified by this scanner.

Similarly, banner information can be incomplete, misleading, or intentionally hidden by a service.

For a proper vulnerability assessment, findings should be manually validated or checked using dedicated vulnerability assessment tools.

---

## Limitations

* Only TCP ports are scanned.
* Banner grabbing currently uses a basic HTTP request.
* Some non-HTTP services may not return useful information to the banner-grabbing method.
* Exploit-DB results are based on service/version search terms.
* The tool does not automatically verify whether an exploit is actually applicable.
* It does not perform authenticated vulnerability testing.
* It does not perform CVE matching or CVSS scoring.
* It does not test for exploitation.
* Exploit-DB's website structure or access behavior may change, which can affect HTML parsing.

---

## Future Improvements

Possible improvements include:

* [ ] Add UDP scanning support.
* [ ] Improve service-specific banner grabbing.
* [ ] Add CVE database/API integration.
* [ ] Map detected software versions to CVE identifiers.
* [ ] Add severity information using CVSS.
* [ ] Improve error handling and request timeouts.
* [ ] Add structured logging.
* [ ] Generate HTML or PDF security reports.
* [ ] Add configurable scan options.
* [ ] Improve result validation to reduce false positives.

---

## Learning Outcomes

Through this project, the following practical concepts were explored:

* Network reconnaissance
* TCP port scanning
* Nmap and service enumeration
* Socket programming
* Banner grabbing
* HTTP requests
* HTML parsing
* Vulnerability research
* Exploit database searching
* JSON data handling
* Python command-line applications
* Basic security assessment workflow

---

## Ethical Use

This project should only be used against:

* Your own computer or server
* A local virtual machine/lab environment
* Systems where you have explicit authorization to perform security testing

Do **not** scan public systems, company infrastructure, or networks belonging to other people without permission.

---

## License

This project is intended for educational and security-learning purposes.

Use it responsibly and only on systems you are authorized to test.
