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

# Navigate to cowrie folder and create virtual environment
python3 -m venv cowrie-env
source cowrie-env/bin/activate

# Install required Python dependencies
pip install -r requirements.txt

# Start the honeypot
twistd cowrie

<\details>

---
