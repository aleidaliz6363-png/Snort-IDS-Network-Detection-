# Snort-IDS-Network-Detection-
SOC-Analyst-IDS-Lab 
# 🛡️ SOC Analyst Lab — Network Intrusion Detection with Snort & TCPdump

**Course:** Securing Your Network  
**Program:** Cybersecurity Professional Bootcamp — University of Michigan  
**Author:** Aleidaliz Ramirez Perez  

---

## 📋 Overview

This project documents a hands-on SOC (Security Operations Center) analyst lab where the objective was to identify and stop malicious network traffic using **TCPdump** for packet sniffing and **Snort IDS** for intrusion detection. Custom Snort rules were written to detect suspicious traffic patterns, and alerts were monitored and verified through the **Snorby** SIEM dashboard.

**Skills demonstrated:**
- Live network traffic sniffing and filtering with TCPdump
- Identifying malicious traffic patterns (port scanning, abnormal TCP headers)
- Writing and deploying custom Snort IDS rules
- Snort configuration and console-mode testing
- SIEM alert monitoring using Snorby GUI
- Linux CLI navigation and file editing

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|------|---------|
| TCPdump | Live packet capture and traffic filtering |
| Snort IDS | Custom intrusion detection rule writing and deployment |
| Snorby | SIEM GUI for alert visualization and monitoring |
| Ubuntu CLI | Linux environment for all commands |
| nano | Text editor for modifying Snort rule files |

---

## 🎯 Objectives

- Sniff incoming network traffic and isolate suspicious packets using TCPdump
- Identify malicious traffic patterns such as port scans and abnormal TCP window sizes
- Write a custom Snort IDS rule to detect and alert on the malicious traffic
- Verify alerts are generated correctly in the Snorby SIEM dashboard
- Retrieve the lab flag from the Snorby Sensors section

---

## 🔬 Step-by-Step Walkthrough

### Step 1: Access the Environment

Logged into the web-based Ubuntu CLI and Snorby GUI provided in the challenge environment and familiarized myself with the network topology and available tools.

---

### Step 2: Sniff Traffic with TCPdump

Filtered out known legitimate traffic to focus on suspicious activity:

```bash
sudo tcpdump port not '(8443 or 22 or 80 or 3306)' -n
```

**Malicious patterns identified:**
- Same source IP repeatedly hitting different destination ports — a classic **port scan signature**
- TCP Window size of exactly **1024 bytes** — an indicator commonly associated with automated scanning tools

Refined the filter to isolate traffic with the suspicious TCP Window size:

```bash
sudo tcpdump port not 8443 and port '(22 or 80 or 3306)' and tcp[14:2] = 1024 -n
```

> 💡 `tcp[14:2]` reads 2 bytes starting at byte offset 14 in the TCP header, which is where the Window Size field lives.

---

### Step 3: Write a Custom Snort IDS Rule

Opened the Snort local rules file:

```bash
nano /etc/snort/rules/local.rules
```

Added the following custom rule to detect the malicious TCP Window size:

```snort
alert tcp any any -> any any (msg:"TCP Window Size 1024"; sid:1000001; rev:1; window:1024;)
```

**Rule breakdown:**

| Component | Meaning |
|-----------|---------|
| `alert` | Generate an alert when matched |
| `tcp any any -> any any` | Match any TCP traffic from any source to any destination |
| `msg:"TCP Window Size 1024"` | Alert message label |
| `sid:1000001` | Unique rule ID (local rules start at 1000001) |
| `rev:1` | Rule revision number |
| `window:1024` | Match packets with TCP Window size of exactly 1024 bytes |

Saved and exited the editor, then tested the rule in Snort console mode:

```bash
sudo snort -c /etc/snort/snort.conf -A console
```

---

### Step 4: Verify Alerts in Snorby

Started Snort to send live alerts to the Snorby SIEM:

```bash
sudo snort -c /etc/snort/snort.conf
```

Logged into the Snorby dashboard:
- **Email:** snorby@example.com
- **Password:** snorby

Confirmed alerts were populating on the dashboard, verifying that Snort was successfully detecting and reporting the malicious traffic in real time.

---

### Step 5: Retrieve the Flag

Navigated to the **Sensors** section in the Snorby GUI and retrieved the flag that appeared as a new sensor entry — confirming successful completion of the lab.

---

## 📌 Key Takeaways

- **TCPdump** is a powerful first-response tool for SOC analysts to quickly isolate suspicious traffic without needing a full SIEM setup.
- A **TCP Window size of 1024 bytes** is a well-known indicator of automated port scanning tools — flagging this pattern is a practical real-world detection technique.
- **Custom Snort rules** allow security teams to tailor intrusion detection to their specific environment and threat landscape, going beyond generic out-of-the-box signatures.
- **SIEM tools like Snorby** provide visual confirmation that detection rules are working and help analysts triage alerts efficiently.
- Always test IDS rules in console mode before deploying to production to avoid false positives or missed detections.
- Using `man pcap-filter` is a valuable habit when building complex TCPdump filters — understanding the documentation leads to more precise and effective packet capture.

---

## 📚 Commands Quick Reference

```bash
# Sniff all traffic excluding known ports
sudo tcpdump port not '(8443 or 22 or 80 or 3306)' -n

# Filter for TCP Window Size 1024
sudo tcpdump port not 8443 and port '(22 or 80 or 3306)' and tcp[14:2] = 1024 -n

# Edit Snort local rules
nano /etc/snort/rules/local.rules

# Test Snort rule in console mode
sudo snort -c /etc/snort/snort.conf -A console

# Run Snort and send alerts to Snorby
sudo snort -c /etc/snort/snort.conf

# View packet filter documentation
man pcap-filter
```

---

## 👩‍💻 Author

**Aleidaliz Ramirez Perez**  
Cybersecurity Professional Bootcamp — University of Michigan  
[LinkedIn](https://linkedin.com/in/aleidaliz-ramirez-perez) | [GitHub](https://github.com/aleidaliz)
