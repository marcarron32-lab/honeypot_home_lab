# 🐝 Cowrie Honeypot Home Lab

---

<div align="center">
<img src="https://img.shields.io/badge/-Honeypot-FFCC00?style=for-the-badge&logoColor=black" />
<img src="https://img.shields.io/badge/-SSH-4D4D4D?style=for-the-badge&logoColor=white" />
<img src="https://img.shields.io/badge/-Linux-E95420?style=for-the-badge&logo=linux&logoColor=white" />
<img src="https://img.shields.io/badge/-VMware-607078?style=for-the-badge&logo=vmware&logoColor=white" />
</div>

---

## 🎯 Objective
<div style="background-color:#f0f4f8;padding:10px;border-radius:8px;">
Deploy a **Cowrie SSH honeypot**, capture attacker behavior, and analyze logs to understand intrusion patterns.  
Strengthen foundational **SOC skills** including monitoring, logging, and attack pattern recognition.
</div>

---

## 🧰 Skills Practiced
<div style="background-color:#e8f5e9;padding:10px;border-radius:8px;">
- Linux system administration  
- Honeypot deployment & monitoring  
- SSH attack and brute-force analysis  
- Security log collection & JSON parsing  
- Command-line investigation (Bash, jq)  
- Virtual machine networking  
- Defensive security awareness
</div>

---

## 🛠️ Tools Used
<div style="background-color:#fff3e0;padding:10px;border-radius:8px;">
<img src="https://img.shields.io/badge/-Ubuntu-E95420?&style=for-the-badge&logo=ubuntu&logoColor=white" />
<img src="https://img.shields.io/badge/-Kali_Linux-557C94?style=for-the-badge&logo=Kali-Linux&logoColor=white" />
<img src="https://img.shields.io/badge/-Linux-FCC624?&style=for-the-badge&logo=linux&logoColor=black" />
<img src="https://img.shields.io/badge/-SSH-4D4D4D?&style=for-the-badge&logoColor=white" />
<img src="https://img.shields.io/badge/-Cowrie-FFCC00?&style=for-the-badge&logoColor=black" />
<img src="https://img.shields.io/badge/-VMware-607078?&style=for-the-badge&logo=vmware&logoColor=white" />
<img src="https://img.shields.io/badge/-Bash-4EAA25?&style=for-the-badge&logo=gnu-bash&logoColor=white" />
<img src="https://img.shields.io/badge/-jq-000000?&style=for-the-badge&logoColor=white" />
</div>

---
## 🏗️ Lab Setup

<details>
<summary>💻 Click to expand: Commands to deploy Cowrie</summary>

```bash
# Clone Cowrie repository
git clone https://github.com/cowrie/cowrie.git

# Change directory to the cowrie
cd cowrie

# Navigate to cowrie folder and create virtual environment
python3 -m venv cowrie-env
source cowrie-env/bin/activate

# Install required Python dependencies
pip install --upgrade pip
pip install -e .

# Start the honeypot
start cowrie
```
</details>

💡 Tip: Verify honeypot is running on port 2222:
netstat -tulnp | grep 2222 or ss -tulnp | grep 2222

![Cowrie Lab Setup Screenshots](screenshots/lab_setup)


---

## 🔍 Attack Simulation

To simulate real-world attacker behavior, SSH connections were initiated from a separate attacker machine targeting the Cowrie honeypot on port **2222**.

### Attack Activities Performed
- Multiple SSH login attempts using common usernames and weak passwords  
- Observation of both **failed** and **successful** authentication attempts  
- Execution of basic Linux commands after successful login  
- Interaction with the fake filesystem provided by Cowrie  

These actions allowed the honeypot to capture realistic attacker behavior without risking the host system.

*Ref 1: Attacker SSH login attempt device 1*  
![Attacker SSH Attempt](screenshots/attack_simulation)

*Ref 2: Attacker SSH login attempt device 2*  
![Attacker SSH Attempt](screenshots/attack_simulation2)

---

## 📄 Log Analysis

Cowrie stores all captured activity in structured **JSON logs**, primarily located in:
cowrie/var/log/cowrie/cowrie.json

```bash
# Change to the directory where logs are stored
cd cowrie/var/log/cowrie
```

The logs were analyzed using `jq` to extract meaningful security events such as login attempts, credentials used, source IP addresses, and executed commands.

### 📄 Example Log Analysis Commands

<details>
<summary>💻 Click to expand: Commands to deploy Cowrie</summary>

```bash
# View all login-related events (clean output)
jq -r 'select(.eventid | startswith("cowrie.login"))' cowrie.json

# Show only failed login attempts with details
jq -r 'select(.eventid=="cowrie.login.failed") |
"TIME: \(.timestamp) | IP: \(.src_ip) | USER: \(.username) | PASS: \(.password)"' cowrie.json

# Show only successful login attempts
jq -r 'select(.eventid=="cowrie.login.success") |
"TIME: \(.timestamp) | IP: \(.src_ip) | USER: \(.username)"' cowrie.json

# Extract all attempted credentials
jq -r 'select(.username != null) |
"USERNAME: \(.username) | PASSWORD: \(.password)"' cowrie.json

# List unique attacker IP addresses
jq -r '.src_ip' cowrie.json | sort | uniq

# Count number of events per attacker IP (basic attack volume)
jq -r '.src_ip' cowrie.json | sort | uniq -c | sort -nr

# Show commands executed by attackers
jq -r 'select(.eventid=="cowrie.command.input") |
"IP: \(.src_ip) | COMMAND: \(.input)"' cowrie.json

# Build a simple attacker activity timeline
jq -r 'select(.eventid | test("login|command")) |
"TIME: \(.timestamp) | IP: \(.src_ip) | EVENT: \(.eventid) | DATA: \(.input // .username // "-")"' cowrie.json
```
</details>

This analysis mimics Tier 1 SOC tasks, where analysts triage authentication events and identify suspicious behavior.

![Log Analysis Screenshots](screenshots/log_analysis)

---
## 📊 Results & Observations

### Results

The Cowrie honeypot successfully captured and recorded attacker activity, providing valuable insight into real-world attack patterns. Key results include:

| Data Captured                  | Description                                                                 |
|--------------------------------|-----------------------------------------------------------------------------|
| Source IP addresses             | Identifies the origin of SSH attacks                                        |
| Attempted usernames & passwords | Shows what credentials attackers try (e.g., `root`, `admin`, `user`)       |
| Session timestamps             | Tracks when attacks occurred                                                |
| Commands executed               | Captures commands run in the fake filesystem                                |
| Automated attack patterns       | Reveals brute-force or bot-driven attack behavior                           |

**Example log extract from `cowrie.json`:**

```json
{
  "eventid": "cowrie.login.success",
  "username": "root",
  "password": "*********",
  "src_ip": "192.168.1.**",
  "timestamp": "2026-01-18T12:34:56"
}
```
### Observations

From the captured data, the following observations were made:
- SSH is a frequent target: Attackers routinely scan and attempt connections on SSH ports
- Weak credentials exploited: Default usernames like root, admin, and user are commonly used
- Command patterns: Attackers often executed reconnaissance commands (ls, pwd, whoami)
- Automated attacks detected: Multiple repeated login attempts suggest bot-driven brute-force behavior
- Value for SOC: Honeypot logs provide actionable intelligence for monitoring, alerting, and incident response

---

## 🧠 Lessons Learned

Through this Cowrie Honeypot lab, several important cybersecurity and SOC skills were reinforced:

- **Honeypot Deployment:** Successfully set up a controlled SSH honeypot environment to attract and monitor attackers.  
- **Log Management:** Learned how to collect, parse, and analyze JSON-based honeypot logs using command-line tools like `jq`.  
- **Attack Recognition:** Identified common attack patterns, including brute-force attempts and reconnaissance commands.  
- **Credential Awareness:** Observed that attackers frequently target weak or default credentials, highlighting the importance of strong authentication.  
- **SOC Practices:** Gained experience in monitoring, documenting, and interpreting attacker activity, simulating Tier 1 SOC analyst tasks.  
- **Defensive Thinking:** Understanding attacker behavior helps in planning proactive defensive strategies and enhancing security posture.  
- **Integration Insight:** Recognized the value of forwarding honeypot logs to a SIEM system for correlation, alerting, and further analysis.  

---

## ✅ Conclusion

The Cowrie Honeypot Home Lab successfully demonstrated the deployment, monitoring, and analysis of a deception-based security control.  

Key takeaways include:

- **Practical SOC Experience:** Simulated real-world attack scenarios and performed tasks similar to a Tier 1 SOC analyst, such as log monitoring and incident observation.  
- **Understanding Attacker Behavior:** Captured attacker credentials, commands, session activity, and automated attack patterns, providing insight into common intrusion techniques.  
- **Importance of Logging:** Structured JSON logs enabled efficient analysis, highlighting the critical role of proper logging in cybersecurity operations.  
- **Foundation for SIEM Integration:** Honeypot logs can be forwarded to SIEM platforms for alerting, correlation, and incident response automation.  
- **Enhanced Defensive Skills:** Hands-on experience strengthened skills in Linux administration, network monitoring, and threat detection, forming a strong foundation for future cybersecurity projects.  

This lab provides a solid starting point for **advanced security operations**, including **SIEM deployment, automated alerts, and proactive defense strategies**, making it an essential component of a cybersecurity portfolio.

---

⚠️ **Disclaimer:** This repository and all content, including the Cowrie Honeypot setup and attack simulations, are intended **for educational and research purposes only**.  
Unauthorized access to systems or networks is illegal. Do **not** attempt to deploy this honeypot on networks or systems that you do not own or have explicit permission to use.

---

