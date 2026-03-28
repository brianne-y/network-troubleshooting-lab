# Network Troubleshooting Lab

Built a network troubleshooting lab in Microsoft Azure to practice systematic diagnosis of common connectivity issues. Simulated 5 real-world failure scenarios — DNS misconfigurations, disabled adapters, subnet mismatches, latency diagnosis, and DHCP failures — and resolved each using core networking tools.

## Tools Used

- Microsoft Azure (Virtual Machines / IaaS)
- Windows Server 2022
- Command Prompt networking utilities (ipconfig, ping, nslookup, tracert, netstat)
- Remote Desktop Protocol (RDP)

---

## Phase 1: Azure VM Deployment

Deployed a Windows Server VM in Azure to serve as the troubleshooting environment.

![VM Deployment In Progress](img/01.png)
*Azure deployment in progress — provisioning VM resources*

![VM Deployment Complete](img/02.png)
*Deployment complete — VM successfully created in the NetworkLab resource group*

![VM Overview Running](img/03.png)
*VM overview showing status "Running" with public IP assigned*

---

## Phase 2: Core Networking Commands

Practiced the five essential networking commands used daily in IT support.

### ipconfig /all — Network Identity Check

![ipconfig /all](img/04.png)
*Baseline network configuration showing IPv4 address (10.0.0.5), subnet mask, default gateway (10.0.0.1), and DNS server*

### Three-Ping Sequence — Connectivity Testing

![Three Ping Sequence](img/05.png)
*Testing connectivity at each layer: gateway → external IP (8.8.8.8) → domain name (google.com)*

### nslookup & tracert — DNS and Route Testing

![nslookup and tracert](img/06.png)
*nslookup resolving google.com through both default and Google's DNS; tracert showing the path to destination*

---

## Scenario 1: DNS Failure

**The Problem:** User reports "I can't get to any websites."

**Diagnosis:**
- Ping to 8.8.8.8 succeeded (internet connection works)
- Ping to google.com failed (DNS resolution broken)
- nslookup confirmed the configured DNS server (1.2.3.4) was unreachable

**Resolution:** Corrected DNS server settings in adapter properties and flushed the DNS cache with `ipconfig /flushdns`.

![DNS Original Settings](img/07.png)
*Original settings — "Obtain DNS server address automatically" selected*

![DNS Intentionally Broken](img/08.png)
*Breaking DNS — set preferred DNS server to fake address 1.2.3.4*

![Browser Can't Reach Google](img/09.png)
*Result — browser shows "can't reach this page" with DNS_PROBE error*

![Ping Diagnosis](img/10.png)
*Diagnosing the issue — ping to 8.8.8.8 works, but ping to google.com fails*

![nslookup Confirms DNS Issue](img/11.png)
*nslookup confirms it — query to 1.2.3.4 times out, but query to 8.8.8.8 resolves successfully*

![DNS Flushed and Fixed](img/12.png)
*Fixed — ran ipconfig /flushdns and ping to google.com now succeeds*

![Google Loads Successfully](img/13.png)
*Verified — Google homepage loads successfully*

---

## Scenario 2: Disabled Network Adapter

**The Problem:** User reports "My computer has no network connection at all."

**Diagnosis:**
- ipconfig shows "Media disconnected"
- Ping returns "General failure" (not "Request timed out")
- Network adapter is disabled

**Resolution:** Re-enabled the network adapter. In this case, had to deploy a new VM to recover RDP access.

![Disable Adapter Warning](img/14.png)
*Disabling the network adapter — Windows warning that it will stop functioning*

![RDP Connection Lost](img/15.png)
*Result — RDP connection immediately lost, "Preparing to reconnect..."*

![New VM Recovery](img/16.png)
*Recovery — deployed a new VM to regain access (lesson learned: use Azure Run Command)*

---

## Scenario 3: Wrong Subnet

**The Problem:** User reports "I can't reach the company server or shared drives."

**Diagnosis:**
- ipconfig shows IP address on wrong network range (192.168.50.100 instead of 10.0.0.x)
- Ping to gateway fails
- Computer is in the "wrong neighborhood"

**Resolution:** Changed IP settings back to "Obtain an IP address automatically" using Azure Run Command.

![Wrong Subnet Configuration](img/17.png)
*Breaking the subnet — manually set IP to 192.168.50.100, completely different range from gateway (10.0.0.1)*

![Connection Lost Wrong Subnet](img/18.png)
*Result — RDP connection lost due to subnet mismatch*

![Azure Run Command Fix](img/19.png)
*Fix — used Azure Portal's Run Command to execute PowerShell script and reset network adapter to DHCP*

![VM Recovered](img/20.png)
*Recovery complete — VM back online after re-deployment*

---

## Scenario 4: Slow Internet (Latency Diagnosis)

**The Problem:** User reports "Everything is loading really slowly."

**Diagnosis:**
- Extended ping (20 packets) to measure response times and packet loss
- tracert to identify which hop is causing the bottleneck

**Resolution:** Network performance was normal in this test — documented baseline metrics for comparison.

![Extended Ping and Tracert](img/21.png)
*Latency diagnosis — ping google.com -n 20 shows consistent 5ms response, 0% packet loss; tracert shows 14 hops to destination*

---

## Scenario 5: DHCP Failure

**The Problem:** User reports "I can't get an IP address" (shows 169.254.x.x or 0.0.0.0).

**Diagnosis:**
- ipconfig shows APIPA address (169.254.x.x) or no IP
- DHCP server not responding or unreachable

**Resolution:** Released and renewed the IP address with `ipconfig /release && ipconfig /renew`.

![DHCP Release Reconnecting](img/22.png)
*DHCP release — connection temporarily lost as IP is released, RDP attempting to reconnect*

![DHCP Renew Success](img/23.png)
*DHCP renew successful — new IP address (10.0.0.5) obtained from DHCP server*

---

## Key Takeaways

- **Systematic troubleshooting wins:** Checking each network layer in order (adapter → gateway → internet → DNS) isolates problems faster than random guessing
- **The ping sequence is essential:** Gateway → 8.8.8.8 → domain name — when one step fails but the previous worked, you've found exactly where the problem is
- **Recognize the symptoms:**
  - 169.254.x.x = DHCP failure (APIPA address)
  - "General failure" = adapter/hardware issue
  - "Request timed out" = connectivity/routing issue
  - IP works but domain doesn't = DNS problem
- **Document before you break:** Always note original settings before making changes — it's your undo button
- **Azure Run Command is a lifesaver:** When you lock yourself out of RDP, you can still run commands through the Azure Portal
