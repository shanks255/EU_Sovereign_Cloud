# EU Sovereign Cloud - Incident Response Plan

## Table of Contents
1. [Overview](#overview)
2. [Incident Classification](#incident-classification)
3. [Response Team Structure](#response-team-structure)
4. [Incident Response Process](#incident-response-process)
5. [Communication Procedures](#communication-procedures)
6. [Technical Response Procedures](#technical-response-procedures)
7. [Legal and Regulatory Requirements](#legal-and-regulatory-requirements)
8. [Post-Incident Activities](#post-incident-activities)
9. [Training and Exercises](#training-and-exercises)
10. [Appendices](#appendices)

## Overview

This Incident Response Plan (IRP) defines the procedures for detecting, analyzing, containing, eradicating, and recovering from security incidents within the EU Sovereign Cloud infrastructure. The plan ensures compliance with EU regulations including GDPR, NIS2, and other applicable frameworks.

### Objectives
- **Minimize Impact**: Reduce business disruption and data loss
- **Preserve Evidence**: Maintain forensic integrity for investigation
-- **Ensure Compliance**: Meet regulatory notification requirements
- **Continuous Improvement**: Learn from incidents to strengthen security

### Scope
This plan covers all security incidents affecting:
- EU Sovereign Cloud infrastructure
- Customer data and services
- Supporting systems and networks
- Third-party integrations

---

## Incident Classification

### Severity Levels

#### Critical (P1)
- **Response Time**: 15 minutes
- **Examples**:
  - Active data breach with confirmed data exfiltration
  - Ransomware affecting critical systems
  - Complete service outage
  - Unauthorized access to customer data
  - Nation-state attack indicators

#### High (P2)
- **Response Time**: 30 minutes
- **Examples**:
  - Suspected data breach (no confirmed exfiltration)
  - Malware infection on critical systems
  - Significant service degradation
  - Unauthorized privilege escalation
  - DDoS attacks affecting availability

#### Medium (P3)
- **Response Time**: 2 hours
- **Examples**:
  - Failed login attempts (brute force)
  - Suspicious network activity
  - Minor service disruptions
  - Policy violations
  - Phishing attempts targeting staff

#### Low (P4)
- **Response Time**: 24 hours
- **Examples**:
  - Spam emails
  - Minor configuration issues
  - Informational security alerts
  - Routine vulnerability scans

### Impact Categories

```
┌─────────────────────────────────────────────────────────┐
│                Impact Assessment Matrix                  │
├─────────────────────────────────────────────────────────┤
│                    │ Low │ Medium │ High │ Critical │    │
├────────────────────┼─────┼────────┼──────┼──────────┤    │
│ Confidentiality    │ P4  │   P3   │  P2  │    P1    │    │
│ Integrity          │ P4  │   P3   │  P2  │    P1    │    │
│ Availability       │ P4  │   P3   │  P2  │    P1    │    │
│ Regulatory Impact  │ P4  │   P3   │  P2  │    P1    │    │
└─────────────────────────────────────────────────────────┘
```

---

## Response Team Structure

### Incident Response Team (IRT)

#### Core Team Members
- **Incident Commander (IC)**: Overall incident coordination
- **Security Analyst**: Technical investigation and analysis
- **System Administrator**: Infrastructure and system response
- **Legal Counsel**: Regulatory and legal guidance
- **Communications Lead**: Internal and external communications

#### Extended Team (As Needed)
- **Forensics Specialist**: Digital forensics and evidence collection
- **Threat Intelligence Analyst**: Threat attribution and context
- **Customer Success Manager**: Customer communication
- **Compliance Officer**: Regulatory compliance oversight
- **External Consultants**: Specialized expertise

### Contact Information

| Role | Primary Contact | Backup Contact | Escalation |
|------|----------------|----------------|------------|
| Incident Commander | +33-1-XXXX-1001 | +33-1-XXXX-1002 | CISO |
| Security Analyst | +33-1-XXXX-2001 | +33-1-XXXX-2002 | Security Manager |
| System Administrator | +33-1-XXXX-3001 | +33-1-XXXX-3002 | Infrastructure Manager |
| Legal Counsel | +33-1-XXXX-4001 | +33-1-XXXX-4002 | General Counsel |

### Escalation Matrix

```
┌─────────────────────────────────────────────────────────┐
│              Escalation Timeline                         │
├─────────────────────────────────────────────────────────┤
│  0-15 min  │ On-call Security Analyst                   │
│  15-30 min │ Incident Commander                         │
│  30-60 min │ Security Manager                           │
│  1-2 hours │ CISO                                       │
│  2-4 hours │ CTO/CIO                                    │
│  4+ hours  │ CEO/Executive Team                         │
└─────────────────────────────────────────────────────────┘
```

---

## Incident Response Process

### Phase 1: Preparation

#### Pre-Incident Activities
1. **Maintain Response Capabilities**
   - Keep incident response tools updated
   - Maintain current contact lists
   - Ensure backup communication channels
   - Regular training and exercises

2. **Monitoring and Detection**
   ```bash
   # Security monitoring tools
   - SIEM: Splunk Enterprise Security
   - EDR: CrowdStrike Falcon
   - Network: Zeek + Suricata
   - Cloud: AWS GuardDuty, Azure Sentinel
   ```

3. **Documentation Readiness**
   - Incident response procedures
   - System documentation
   - Network diagrams
   - Contact information

### Phase 2: Identification

#### Detection Sources
- **Automated Alerts**: SIEM, IDS/IPS, EDR systems
- **User Reports**: Help desk tickets, direct reports
- **Third-Party Notifications**: Threat intelligence, law enforcement
- **Routine Monitoring**: Log analysis, vulnerability scans

#### Initial Assessment
1. **Verify the Incident**
   ```bash
   # Check alert validity
   grep -i "suspicious_activity" /var/log/security.log
   
   # Correlate with other systems
   curl -X GET "https://siem.eu-cloud.local/api/events" \
     -H "Authorization: Bearer <token>" \
     -d "timerange=1h&severity=high"
   ```

2. **Classify Severity**
   - Assess impact on CIA triad
   - Determine affected systems
   - Estimate business impact
   - Check regulatory implications

3. **Activate Response Team**
   ```bash
   # Automated notification
   curl -X POST "https://alerting.eu-cloud.local/api/incidents" \
     -H "Content-Type: application/json" \
     -d '{
       "severity": "P1",
       "title": "Security Incident Detected",
       "description": "Unauthorized access detected",
       "affected_systems": ["web-server-01", "database-01"]
     }'
   ```

### Phase 3: Containment

#### Short-term Containment
1. **Immediate Actions**
   ```bash
   # Isolate affected systems
   iptables -A INPUT -s <malicious-ip> -j DROP
   iptables -A OUTPUT -d <malicious-ip> -j DROP
   
   # Disable compromised accounts
   openstack user set --disable <user-id>
   kubectl delete serviceaccount <compromised-sa>
   
   # Quarantine infected systems
   openstack server set --property quarantine=true <server-id>
   ```

2. **Evidence Preservation**
   ```bash
   # Create forensic images
   dd if=/dev/sda of=/forensics/incident-$(date +%Y%m%d)/system.img bs=4M
   
   # Capture memory dump
   lime-forensics /dev/mem /forensics/incident-$(date +%Y%m%d)/memory.dump
   
   # Preserve network traffic
   tcpdump -i eth0 -w /forensics/incident-$(date +%Y%m%d)/network.pcap
   ```

#### Long-term Containment
1. **System Hardening**
   ```bash
   # Apply security patches
   ansible-playbook -i inventory security-patches.yml
   
   # Update firewall rules
   ansible-playbook -i inventory firewall-update.yml
   
   # Rotate credentials
   ansible-playbook -i inventory credential-rotation.yml
   ```

2. **Monitoring Enhancement**
   ```bash
   # Deploy additional monitoring
   kubectl apply -f enhanced-monitoring.yaml
   
   # Increase log verbosity
   sed -i 's/LogLevel INFO/LogLevel DEBUG/' /etc/ssh/sshd_config
   systemctl reload sshd
   ```

### Phase 4: Eradication

#### Root Cause Analysis
1. **Forensic Investigation**
   ```bash
   # Analyze system logs
   grep -E "(failed|error|unauthorized)" /var/log/auth.log
   
   # Check file integrity
   aide --check
   
   # Analyze malware samples
   clamav-scan /suspected/malware/path
   ```

2. **Vulnerability Assessment**
   ```bash
   # Run vulnerability scans
   nmap -sV --script vuln <target-range>
   
   # Check for misconfigurations
   lynis audit system
   
   # Analyze security controls
   ansible-playbook -i inventory security-audit.yml
   ```

#### Threat Removal
1. **Malware Removal**
   ```bash
   # Remove malicious files
   find /var/www -name "*.php.suspected" -delete
   
   # Clean registry (Windows systems)
   reg delete "HKLM\Software\Malware" /f
   
   # Remove persistence mechanisms
   crontab -r -u <compromised-user>
   ```

2. **System Restoration**
   ```bash
   # Restore from clean backups
   pg_restore -d production /backups/clean/database.dump
   
   # Rebuild compromised systems
   ansible-playbook -i inventory system-rebuild.yml
   
   # Update security configurations
   ansible-playbook -i inventory security-hardening.yml
   ```

### Phase 5: Recovery

#### Service Restoration
1. **Gradual Restoration**
   ```bash
   # Start with non-critical services
   systemctl start <non-critical-service>
   
   # Monitor for anomalies
   tail -f /var/log/<service>.log
   
   # Gradually restore critical services
   systemctl start <critical-service>
   ```

2. **Validation Testing**
   ```bash
   # Functional testing
   curl -I https://api.eu-cloud.local/health
   
   # Security testing
   nmap -sS <restored-system>
   
   # Performance testing
   ab -n 1000 -c 10 https://web.eu-cloud.local/
   ```

#### Monitoring and Validation
1. **Enhanced Monitoring**
   ```bash
   # Deploy additional sensors
   kubectl apply -f incident-monitoring.yaml
   
   # Increase alert sensitivity
   curl -X PUT "https://siem.eu-cloud.local/api/rules/<rule-id>" \
     -d '{"threshold": 1, "enabled": true}'
   ```

2. **Continuous Validation**
   ```bash
   # Automated security checks
   while true; do
     nmap -sS <critical-systems>
     sleep 300
   done
   ```

### Phase 6: Lessons Learned

#### Post-Incident Review
1. **Timeline Reconstruction**
   - Document incident timeline
   - Identify detection gaps
   - Analyze response effectiveness
   - Calculate business impact

2. **Improvement Identification**
   - Technical improvements needed
   - Process enhancements
   - Training requirements
   - Tool upgrades

---

## Communication Procedures

### Internal Communications

#### Notification Matrix
| Audience | Timeframe | Method | Content |
|----------|-----------|---------|---------|
| IRT Team | Immediate | Phone/SMS | Alert details, initial assessment |
| Management | 30 minutes | Phone/Email | Executive summary, impact |
| IT Staff | 1 hour | Email/Slack | Technical details, actions needed |
| All Staff | 2 hours | Email | General awareness, precautions |

#### Communication Templates

**Initial Alert (P1/P2)**
```
SUBJECT: [URGENT] Security Incident - Immediate Response Required

Incident ID: INC-2025-001
Severity: P1 - Critical
Detected: 2025-11-20 07:15 UTC
Affected Systems: [List systems]

Initial Assessment:
- [Brief description of incident]
- [Potential impact]
- [Immediate actions taken]

Response Team Activated:
- Incident Commander: [Name, Contact]
- Next Update: [Time]

Do not discuss this incident outside the response team.
```

### External Communications

#### Customer Notifications
1. **Notification Triggers**
   - Data breach affecting customer data
   - Service outage > 4 hours
   - Security control failures
   - Regulatory reporting requirements

2. **Notification Timeline**
   ```
   ┌─────────────────────────────────────────────────────────┐
   │            Customer Notification Timeline                │
   ├─────────────────────────────────────────────────────────┤
   │  0-4 hours  │ Internal assessment and containment      │
   │  4-8 hours  │ Customer notification (if required)      │
   │  24 hours   │ Detailed update to affected customers    │
   │  72 hours   │ Final report with remediation steps      │
   └─────────────────────────────────────────────────────────┘
   ```

#### Regulatory Notifications

**GDPR Requirements**
- **Timeline**: Within 72 hours of becoming aware
- **Authority**: Relevant EU Data Protection Authority
- **Content**: Nature of breach, categories of data, number of individuals

**NIS2 Requirements**
- **Early Warning**: Within 24 hours
- **Incident Notification**: Within 72 hours
- **Final Report**: Within 1 month

---

## Technical Response Procedures

### Evidence Collection

#### Digital Forensics Checklist
1. **System State Preservation**
   ```bash
   # Capture system state
   ps aux > /forensics/processes.txt
   netstat -an > /forensics/network.txt
   lsof > /forensics/open_files.txt
   
   # Capture memory
   dd if=/dev/mem of=/forensics/memory.dump bs=1M
   
   # Hash critical files
   find /etc /var/log -type f -exec sha256sum {} \; > /forensics/hashes.txt
   ```

2. **Network Evidence**
   ```bash
   # Capture network traffic
   tcpdump -i any -w /forensics/network_$(date +%Y%m%d_%H%M).pcap
   
   # Export firewall logs
   iptables -L -n -v > /forensics/firewall_rules.txt
   
   # Capture DNS queries
   dig @8.8.8.8 suspicious-domain.com > /forensics/dns_queries.txt
   ```

3. **Log Collection**
   ```bash
   # System logs
   journalctl --since "2025-11-20 00:00:00" > /forensics/system.log
   
   # Security logs
   grep -E "(authentication|authorization|failed)" /var/log/auth.log > /forensics/security.log
   
   # Application logs
   find /var/log -name "*.log" -newer /tmp/incident_start -exec cp {} /forensics/ \;
   ```

### Malware Analysis

#### Safe Analysis Environment
```bash
# Create isolated analysis VM
openstack server create \
  --flavor analysis.medium \
  --image ubuntu-forensics \
  --network isolated-analysis \
  --security-group analysis-sg \
  malware-analysis-$(date +%Y%m%d)

# Transfer suspicious files
scp suspicious_file.exe analyst@analysis-vm:/samples/
```

#### Analysis Procedures
1. **Static Analysis**
   ```bash
   # File information
   file suspicious_file.exe
   strings suspicious_file.exe | grep -E "(http|ftp|\.exe|\.dll)"
   
   # Hash analysis
   sha256sum suspicious_file.exe
   curl "https://www.virustotal.com/vtapi/v2/file/report?apikey=<key>&resource=<hash>"
   ```

2. **Dynamic Analysis**
   ```bash
   # Monitor system calls
   strace -o /analysis/syscalls.log ./suspicious_file.exe
   
   # Monitor network activity
   tcpdump -i eth0 -w /analysis/network.pcap &
   ./suspicious_file.exe
   ```

### Threat Intelligence Integration

#### IOC Collection and Sharing
```bash
# Extract IOCs
grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b" /var/log/security.log > iocs_ips.txt
grep -oE "[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}" /var/log/security.log > iocs_domains.txt

# Share with threat intelligence platforms
curl -X POST "https://threatintel.eu-cloud.local/api/iocs" \
  -H "Content-Type: application/json" \
  -d @iocs.json
```

---

## Legal and Regulatory Requirements

### Evidence Handling

#### Chain of Custody
1. **Documentation Requirements**
   - Who collected the evidence
   - When it was collected
   - Where it was stored
   - Who had access to it

2. **Evidence Storage**
   ```bash
   # Create evidence container
   mkdir -p /evidence/case-$(date +%Y%m%d)/
   
   # Calculate hashes
   sha256sum evidence_file > evidence_file.sha256
   
   # Encrypt evidence
   gpg --cipher-algo AES256 --compress-algo 1 --symmetric evidence_file
   ```

### Regulatory Compliance

#### GDPR Compliance Checklist
- [ ] Incident affects personal data
- [ ] Risk assessment completed
- [ ] DPA notification prepared (if required)
- [ ] Individual notifications planned (if required)
- [ ] Documentation of technical and organizational measures

#### NIS2 Compliance Checklist
- [ ] Incident classification completed
- [ ] Early warning sent (if required)
- [ ] Incident notification prepared
- [ ] Impact assessment documented
- [ ] Response measures documented

---

## Post-Incident Activities

### Incident Documentation

#### Incident Report Template
```markdown
# Incident Report: INC-2025-001

## Executive Summary
- **Incident Type**: [Data Breach/Service Outage/etc.]
- **Severity**: [P1/P2/P3/P4]
- **Duration**: [Start time - End time]
- **Impact**: [Business impact description]

## Timeline
| Time | Event | Action Taken |
|------|-------|--------------|
| 07:15 | Initial detection | Alert triggered |
| 07:20 | Team activation | IRT assembled |
| 07:30 | Containment | Systems isolated |

## Root Cause Analysis
- **Primary Cause**: [Technical/Process/Human error]
- **Contributing Factors**: [List factors]
- **Evidence**: [Supporting evidence]

## Response Effectiveness
- **What Worked Well**: [Successes]
- **Areas for Improvement**: [Gaps identified]
- **Lessons Learned**: [Key takeaways]

## Recommendations
1. [Technical improvements]
2. [Process enhancements]
3. [Training needs]
```

### Improvement Implementation

#### Action Item Tracking
| Action | Owner | Due Date | Status | Priority |
|--------|-------|----------|---------|----------|
| Update firewall rules | Network Team | 2025-12-01 | Open | High |
| Enhance monitoring | Security Team | 2025-11-25 | In Progress | Medium |
| Staff training | HR/Security | 2025-12-15 | Open | Low |

---

## Training and Exercises

### Training Program

#### Role-Based Training
1. **All Staff** (Annual)
   - Security awareness
   - Incident reporting procedures
   - Communication protocols

2. **IT Staff** (Quarterly)
   - Technical response procedures
   - Tool usage
   - Evidence handling

3. **IRT Members** (Monthly)
   - Advanced response techniques
   - Forensics procedures
   - Legal requirements

### Tabletop Exercises

#### Exercise Scenarios
1. **Ransomware Attack**
   - Objective: Test containment and recovery
   - Frequency: Quarterly
   - Participants: Full IRT

2. **Data Breach**
   - Objective: Test notification procedures
   - Frequency: Semi-annually
   - Participants: IRT + Legal + Communications

3. **Insider Threat**
   - Objective: Test investigation procedures
   - Frequency: Annually
   - Participants: IRT + HR + Legal

#### Exercise Evaluation
```markdown
# Exercise Evaluation: EX-2025-Q4

## Scenario
[Description of exercise scenario]

## Objectives Met
- [x] Objective 1: Response time < 30 minutes
- [ ] Objective 2: Proper evidence collection
- [x] Objective 3: Effective communication

## Observations
- **Strengths**: [What worked well]
- **Weaknesses**: [Areas needing improvement]
- **Recommendations**: [Specific improvements]

## Action Items
1. [Improvement action 1]
2. [Improvement action 2]
```

---

## Appendices

### Appendix A: Contact Information

#### Internal Contacts
| Role | Name | Phone | Email | Backup |
|------|------|-------|-------|---------|
| CISO | [Name] | +33-1-XXXX-1000 | ciso@eu-cloud.local | Deputy CISO |
| Security Manager | [Name] | +33-1-XXXX-2000 | sec-mgr@eu-cloud.local | Senior Analyst |
| Infrastructure Manager | [Name] | +33-1-XXXX-3000 | infra-mgr@eu-cloud.local | Lead SysAdmin |

#### External Contacts
| Organization | Contact | Phone | Email | Purpose |
|--------------|---------|-------|-------|---------|
| CERT-EU | SOC | +32-2-XXX-XXXX | cert@cert.europa.eu | Threat intelligence |
| Local Police | Cybercrime Unit | +33-1-XXX-XXXX | cyber@police.fr | Criminal investigation |
| Legal Counsel | [Firm Name] | +33-1-XXX-XXXX | legal@firm.com | Legal advice |

### Appendix B: Technical Tools

#### Incident Response Tools
```bash
# Forensics Tools
- Volatility: Memory analysis
- Autopsy: Disk analysis
- Wireshark: Network analysis
- YARA: Malware detection

# Response Tools
- Ansible: Automated response
- Kubernetes: Container orchestration
- OpenStack: Infrastructure management
- Terraform: Infrastructure as code

# Communication Tools
- Slack: Team communication
- PagerDuty: Alert management
- Zoom: Video conferencing
- SharePoint: Document collaboration
```

### Appendix C: Legal Framework

#### Applicable Regulations
- **GDPR**: General Data Protection Regulation
- **NIS2**: Network and Information Security Directive
- **ISO 27035**: Incident management standard
- **NIST CSF**: Cybersecurity Framework

#### Notification Requirements
| Regulation | Timeline | Authority | Content |
|------------|----------|-----------|---------|
| GDPR | 72 hours | DPA | Breach details, impact, measures |
| NIS2 | 24/72 hours | CSIRT | Incident details, response actions |

### Appendix D: Incident Response Playbooks

#### Quick Reference Cards
1. **Malware Incident**
   - Isolate → Analyze → Eradicate → Recover
   - Tools: EDR, Antivirus, Forensics
   - Escalation: Security Manager

2. **Data Breach**
   - Contain → Assess → Notify → Investigate
   - Legal: GDPR/NIS2 requirements
   - Escalation: CISO + Legal

3. **DDoS Attack**
   - Mitigate → Analyze → Block → Monitor
   - Tools: WAF, Rate limiting, CDN
   - Escalation: Infrastructure Manager

---

*Document Classification: CONFIDENTIAL*
*Last Updated: November 2025*
*Document Version: 1.0*
*Next Review: February 2026*
*Approved by: CISO, Legal Counsel*