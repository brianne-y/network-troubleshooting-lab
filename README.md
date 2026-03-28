
# Network Troubleshooting Lab

## What is this project?

I built this lab to get hands-on experience with the diagnostic process used in professional IT support. Instead of just memorizing commands, I wanted to actually break things on purpose and fix them — the way real troubleshooting works.

This project demonstrates how I systematically diagnose and resolve common network failures using core command-line tools.

## The Tools I Used

- **Microsoft Azure:** My cloud environment where the server lives.
- **Windows Server 2022:** The operating system I troubleshot.
- **Command Prompt Utilities:** ipconfig, ping, nslookup, tracert, and netstat.
- **Remote Desktop (RDP):** How I connected to the server from my own computer.

---

## What I Did (Step-by-Step)

### 1. Building the Lab Environment

I started by deploying a Windows Server VM in Azure. This gave me a controlled environment to intentionally cause network failures and practice resolving them without affecting any real systems.

<table>
  <tr>
    <td><img src="img/Network Troubleshooting Lab/01-phase1-vm-deployment-in-progress.png" width="400"/></td>
    <td><img src="img/Network Troubleshooting Lab/02-phase1-vm-deployment-complete.png" width="400"/></td>
  </tr>
  <tr>
    <td align="center"><em>VM deployment in progress</em></td>
    <td align="center"><em>Deployment complete and ready</em></td>
  </tr>
</table>

---

### 2. Establishing a Baseline Configuration

Before breaking anything, I documented the working network configuration. This is critical — you can't fix what's broken if you don't know what "working" looks like.

<table>
  <tr>
    <td><img src="img/Network Troubleshooting Lab/04-phase2-ipconfig-all-baseline.png" width="400"/></td>
    <td><img src="img/Network Troubleshooting Lab/05-phase2-three-ping-sequence.png" width="400"/></td>
  </tr>
  <tr>
    <td align="center"><em>Baseline: ipconfig /all output</em></td>
    <td align="center"><em>Three-ping sequence confirming connectivity</em></td>
  </tr>
</table>

---

### 3. Scenario 1: DNS Failure

I simulated a DNS misconfiguration by pointing the server to a non-existent DNS address (1.2.3.4). This is one of the most common issues reported to help desks: "I can't get to any websites."

<table>
  <tr>
    <td><img src="img/Network Troubleshooting Lab/07-scenario1-dns-original-settings.png" width="400"/></td>
    <td><img src="img/Network Troubleshooting Lab/08-scenario1-dns-broken-1234.png" width="400"/></td>
  </tr>
  <tr>
    <td align="center"><em>Original DNS settings (automatic)</em></td>
    <td align="center"><em>Misconfigured DNS (pointing to 1.2.3.4)</em></td>
  </tr>
</table>

<table>
  <tr>
    <td><img src="img/Network Troubleshooting Lab/10-scenario1-ping-diagnosis-dns-failure.png" width="400"/></td>
    <td><img src="img/Network Troubleshooting Lab/11-scenario1-nslookup-confirms-dns-issue.png" width="400"/></td>
  </tr>
  <tr>
    <td align="center"><em>Diagnosis: IP ping works, domain ping fails</em></td>
    <td align="center"><em>nslookup confirms DNS is unreachable</em></td>
  </tr>
</table>

<table>
  <tr>
    <td><img src="img/Network Troubleshooting Lab/12-scenario1-dns-flushed-fixed.png" width="400"/></td>
    <td><img src="img/Network Troubleshooting Lab/13-scenario1-google-loads-success.png" width="400"/></td>
  </tr>
  <tr>
    <td align="center"><em>Resolution: DNS flushed, connectivity restored</em></td>
    <td align="center"><em>Verified: Google loads successfully</em></td>
  </tr>
</table>

---

### 4. Scenario 2: Disabled Network Adapter

I disabled the network adapter to simulate a hardware or driver failure. This immediately terminated my RDP session — demonstrating why it's important to have an alternate recovery method.

<table>
  <tr>
    <td><img src="img/Network Troubleshooting Lab/14-scenario2-disable-adapter-warning.png" width="400"/></td>
    <td><img src="img/Network Troubleshooting Lab/15-scenario2-rdp-connection-lost.png" width="400"/></td>
  </tr>
  <tr>
    <td align="center"><em>Warning before disabling adapter</em></td>
    <td align="center"><em>RDP connection immediately lost</em></td>
  </tr>
</table>

---

### 5. Scenario 3: Wrong Subnet Configuration

I manually assigned an IP address (192.168.50.100) that was on a completely different network range than the gateway (10.0.0.1). This simulates what happens when someone incorrectly configures a static IP.

<table>
  <tr>
    <td><img src="img/Network Troubleshooting Lab/17-scenario3-wrong-subnet-192168.png" width="400"/></td>
    <td><img src="img/Network Troubleshooting Lab/18-scenario3-connection-lost.png" width="400"/></td>
  </tr>
  <tr>
    <td align="center"><em>Misconfigured IP on wrong subnet</em></td>
    <td align="center"><em>Connection lost due to subnet mismatch</em></td>
  </tr>
</table>

<table>
  <tr>
    <td><img src="img/Network Troubleshooting Lab/19-scenario3-azure-run-command-fix.png" width="400"/></td>
    <td><img src="img/Network Troubleshooting Lab/20-scenario3-vm-recovered.png" width="400"/></td>
  </tr>
  <tr>
    <td align="center"><em>Recovery via Azure Run Command</em></td>
    <td align="center"><em>VM successfully restored</em></td>
  </tr>
</table>

---

### 6. Scenario 4: Latency Diagnosis

I used extended ping tests and traceroute to measure network performance. This is the diagnostic approach when users report that "everything is slow."

<img src="img/Network Troubleshooting Lab/21-scenario4-extended-ping-tracert.png" width="600"/>

*Extended ping (20 packets): 0% loss, 5ms average | Traceroute: 14 hops to destination*

---

### 7. Scenario 5: DHCP Failure

I released and renewed the IP address to simulate DHCP issues. In a real environment, a 169.254.x.x address indicates the DHCP server is unreachable.

<table>
  <tr>
    <td><img src="img/Network Troubleshooting Lab/22-scenario5-dhcp-release-reconnecting.png" width="400"/></td>
    <td><img src="img/Network Troubleshooting Lab/23-scenario5-dhcp-renew-success.png" width="400"/></td>
  </tr>
  <tr>
    <td align="center"><em>Connection interrupted during release</em></td>
    <td align="center"><em>New IP successfully obtained</em></td>
  </tr>
</table>

---

## Key Concepts I Focused On

- **Systematic Diagnosis:** Checking each network layer in order (adapter → gateway → internet → DNS) to isolate the problem.
- **Pattern Recognition:** Learning what specific error messages indicate (169.254.x.x = DHCP failure, "General failure" = adapter issue).
- **Documentation:** Recording baseline configurations before making changes.
- **Remote Recovery:** Using Azure Run Command when locked out of RDP.

---

## What I Learned

- How to methodically diagnose network issues instead of guessing.
- How to interpret command output to identify the exact point of failure.
- **Most importantly:** How to recover from situations where I accidentally locked myself out of the system.

---

## Conclusion: Why This Lab Matters

Building this environment taught me that network troubleshooting is not about memorizing solutions — it's about understanding layers. Each command tests a specific part of the network stack, and when one test fails but the previous one succeeded, you've found exactly where the problem is.

Through this project, I gained confidence in:

1. **The Diagnostic Process:** I now approach network issues systematically instead of randomly trying fixes.
2. **Command-Line Proficiency:** I'm comfortable using ipconfig, ping, nslookup, tracert, and netstat to gather diagnostic information.
3. **Real-World Problem Solving:** I intentionally broke things and fixed them — which is exactly how troubleshooting works in production environments.

This lab has prepared me to walk into a help desk role and confidently diagnose the most common network issues users encounter.
