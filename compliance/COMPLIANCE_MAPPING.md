[🏠 Home](../index.md) > [✅ Compliance](COMPLIANCE_MAPPING.md)

---

# Regulatory Compliance Mapping

## Overview
This document maps the EU Sovereign Cloud architecture and controls to key European Union regulations and international standards. It demonstrates how the platform achieves and maintains compliance across multiple regulatory frameworks.

## Compliance Framework

```
┌─────────────────────────────────────────────────────────────────┐
│              EU Sovereign Cloud Compliance Stack                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │              EU Regulations (Mandatory)                   │  │
│  │  GDPR • NIS2 • DORA • eIDAS • Cybersecurity Act          │  │
│  └──────────────────────────────────────────────────────────┘  │
│                              ▲                                   │
│  ┌──────────────────────────┼───────────────────────────────┐  │
│  │         International Standards (Certification)           │  │
│  │  ISO 27001 • ISO 27017 • ISO 27018 • SOC 2               │  │
│  └──────────────────────────────────────────────────────────┘  │
│                              ▲                                   │
│  ┌──────────────────────────┼───────────────────────────────┐  │
│  │         Industry Standards (Best Practices)               │  │
│  │  CSA STAR • PCI DSS • NIST • CIS Benchmarks              │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
```

## 1. GDPR (General Data Protection Regulation)

### 1.1 GDPR Principles Mapping

| GDPR Principle | Implementation | Evidence |
|----------------|----------------|----------|
| **Lawfulness, Fairness, Transparency** | Clear privacy notices, consent management, transparent processing | Privacy policy, consent logs, processing records |
| **Purpose Limitation** | Data collected only for specified purposes, no secondary use without consent | Data classification, purpose tags, access controls |
| **Data Minimization** | Collect only necessary data, automated data reduction | Data collection policies, minimization rules |
| **Accuracy** | Data quality controls, update mechanisms, correction workflows | Data quality metrics, update logs |
| **Storage Limitation** | Defined retention periods, automated deletion | Retention policies, deletion logs |
| **Integrity & Confidentiality** | Encryption, access controls, security monitoring | Encryption certificates, access logs, security reports |
| **Accountability** | Documentation, audit trails, DPO oversight | Compliance reports, audit logs, DPO records |

### 1.2 GDPR Rights Implementation

```
┌─────────────────────────────────────────────────────┐
│         GDPR Data Subject Rights                     │
├─────────────────────────────────────────────────────┤
│                                                       │
│  Right to Access (Art. 15)                          │
│  ├─ Self-service portal                              │
│  ├─ API for data export                              │
│  └─ Response within 30 days                          │
│                                                       │
│  Right to Rectification (Art. 16)                    │
│  ├─ Update interfaces                                │
│  ├─ Automated propagation                            │
│  └─ Verification workflows                           │
│                                                       │
│  Right to Erasure (Art. 17)                          │
│  ├─ Deletion workflows                               │
│  ├─ Verification of deletion                         │
│  └─ Certificate of destruction                       │
│                                                       │
│  Right to Restriction (Art. 18)                      │
│  ├─ Processing suspension                            │
│  ├─ Access flagging                                  │
│  └─ Temporary holds                                  │
│                                                       │
│  Right to Data Portability (Art. 20)                 │
│  ├─ Machine-readable export (JSON, CSV)              │
│  ├─ Standardized formats                             │
│  └─ Direct transfer capability                       │
│                                                       │
│  Right to Object (Art. 21)                           │
│  ├─ Opt-out mechanisms                               │
│  ├─ Processing cessation                             │
│  └─ Marketing preferences                            │
│                                                       │
└─────────────────────────────────────────────────────┘
```

### 1.3 GDPR Technical Measures

| Requirement | Control | Implementation |
|-------------|---------|----------------|
| Data Protection by Design | Privacy-first architecture | Pseudonymization, encryption by default, minimal data collection |
| Data Protection by Default | Restrictive default settings | Opt-in consent, minimal data sharing, privacy controls |
| Data Protection Impact Assessment | DPIA process | Risk assessment templates, automated DPIA triggers |
| Data Breach Notification | 72-hour notification | Automated breach detection, notification workflows |
| Data Processing Records | Article 30 records | Automated processing inventory, audit trails |
| Data Protection Officer | DPO appointment | Dedicated DPO, contact information, oversight processes |

## 2. NIS2 Directive (Network and Information Security)

### 2.1 NIS2 Requirements Mapping

| NIS2 Requirement | Implementation | Evidence |
|------------------|----------------|----------|
| **Risk Management** | Comprehensive risk assessment and management | Risk register, assessment reports, mitigation plans |
| **Incident Handling** | 24/7 SOC, incident response procedures | Incident logs, response times, post-incident reports |
| **Business Continuity** | DR plans, backup strategies, failover testing | DR documentation, test results, RTO/RPO metrics |
| **Supply Chain Security** | Vendor risk assessment, secure procurement | Vendor assessments, contracts, security requirements |
| **Security Measures** | Multi-layered security controls | Security architecture, control documentation |
| **Encryption** | End-to-end encryption, key management | Encryption policies, key management procedures |
| **Access Control** | IAM, MFA, least privilege | Access policies, authentication logs |
| **Vulnerability Management** | Regular scanning, patching, pen testing | Scan reports, patch logs, pen test results |

### 2.2 NIS2 Incident Reporting

```
┌─────────────────────────────────────────────────────┐
│         NIS2 Incident Reporting Timeline             │
├─────────────────────────────────────────────────────┤
│                                                       │
│  Early Warning (within 24 hours)                     │
│  ├─ Incident detection                               │
│  ├─ Initial assessment                               │
│  └─ Notification to authorities                      │
│                                                       │
│  Incident Notification (within 72 hours)             │
│  ├─ Detailed incident information                    │
│  ├─ Impact assessment                                │
│  └─ Initial response actions                         │
│                                                       │
│  Final Report (within 1 month)                       │
│  ├─ Root cause analysis                              │
│  ├─ Remediation actions                              │
│  └─ Lessons learned                                  │
│                                                       │
└─────────────────────────────────────────────────────┘
```

## 3. DORA (Digital Operational Resilience Act)

### 3.1 DORA Pillars Mapping

| DORA Pillar | Implementation | Evidence |
|-------------|----------------|----------|
| **ICT Risk Management** | Comprehensive risk framework | Risk policies, assessments, treatment plans |
| **Incident Management** | Incident response procedures, classification | Incident logs, response procedures, metrics |
| **Digital Resilience Testing** | Regular testing, threat-led penetration testing | Test plans, results, remediation tracking |
| **Third-Party Risk** | Vendor management, contractual requirements | Vendor assessments, contracts, monitoring |
| **Information Sharing** | Threat intelligence sharing | Threat feeds, sharing agreements, reports |

### 3.2 DORA Testing Requirements

```
┌─────────────────────────────────────────────────────┐
│         DORA Resilience Testing Program              │
├─────────────────────────────────────────────────────┤
│                                                       │
│  Vulnerability Assessments                           │
│  ├─ Frequency: Quarterly                             │
│  ├─ Scope: All systems                               │
│  └─ Remediation: Risk-based prioritization          │
│                                                       │
│  Penetration Testing                                 │
│  ├─ Frequency: Annual                                │
│  ├─ Type: Threat-led (TLPT)                         │
│  └─ Scope: Critical systems                          │
│                                                       │
│  Scenario-Based Testing                              │
│  ├─ Frequency: Semi-annual                           │
│  ├─ Scenarios: Cyber attacks, failures               │
│  └─ Validation: Recovery procedures                  │
│                                                       │
│  Red Team Exercises                                  │
│  ├─ Frequency: Annual                                │
│  ├─ Scope: End-to-end attack simulation             │
│  └─ Objective: Test detection and response          │
│                                                       │
└─────────────────────────────────────────────────────┘
```

## 4. eIDAS (Electronic Identification and Trust Services)

### 4.1 eIDAS Implementation

| eIDAS Component | Implementation | Evidence |
|-----------------|----------------|----------|
| **Electronic Identification** | Integration with national eID schemes | eID connectors, authentication logs |
| **Trust Services** | Qualified trust service providers | TSP agreements, certificates |
| **Electronic Signatures** | Qualified electronic signatures support | Signature validation, audit trails |
| **Electronic Seals** | Document sealing capabilities | Seal validation, integrity checks |
| **Time Stamps** | Qualified time stamping | Time stamp validation, accuracy logs |
| **Registered Delivery** | Secure electronic delivery | Delivery receipts, proof of delivery |

### 4.2 eIDAS Assurance Levels

```
┌─────────────────────────────────────────────────────┐
│         eIDAS Assurance Levels                       │
├─────────────────────────────────────────────────────┤
│                                                       │
│  Level of Assurance (LoA) Low                        │
│  ├─ Basic identity verification                      │
│  ├─ Single-factor authentication                     │
│  └─ Use case: Low-risk transactions                 │
│                                                       │
│  Level of Assurance (LoA) Substantial                │
│  ├─ Enhanced identity verification                   │
│  ├─ Two-factor authentication                        │
│  └─ Use case: Standard transactions                 │
│                                                       │
│  Level of Assurance (LoA) High                       │
│  ├─ Rigorous identity verification                   │
│  ├─ Multi-factor authentication                      │
│  ├─ Hardware security tokens                         │
│  └─ Use case: High-risk transactions                │
│                                                       │
└─────────────────────────────────────────────────────┘
```

## 5. ISO 27001 (Information Security Management)

### 5.1 ISO 27001 Controls Mapping

| Control Domain | Controls Implemented | Evidence |
|----------------|---------------------|----------|
| **A.5 Information Security Policies** | Security policy, review procedures | Policy documents, approval records |
| **A.6 Organization of Information Security** | Roles, responsibilities, segregation of duties | Org charts, job descriptions, access matrices |
| **A.7 Human Resource Security** | Background checks, training, termination procedures | HR records, training logs, exit checklists |
| **A.8 Asset Management** | Asset inventory, classification, handling | Asset register, classification labels |
| **A.9 Access Control** | Access policy, user management, privilege management | Access policies, user lists, privilege logs |
| **A.10 Cryptography** | Encryption policy, key management | Encryption standards, key management procedures |
| **A.11 Physical Security** | Perimeter security, access controls, equipment security | Security logs, access records, equipment inventory |
| **A.12 Operations Security** | Change management, capacity management, malware protection | Change logs, capacity reports, AV logs |
| **A.13 Communications Security** | Network controls, segregation, transfer policies | Network diagrams, transfer logs |
| **A.14 System Acquisition** | Security requirements, secure development | Requirements docs, SDLC procedures |
| **A.15 Supplier Relationships** | Supplier security, agreements, monitoring | Supplier assessments, contracts, reviews |
| **A.16 Incident Management** | Incident procedures, reporting, evidence collection | Incident logs, procedures, forensic reports |
| **A.17 Business Continuity** | BCP, DR procedures, testing | BCP documents, DR plans, test results |
| **A.18 Compliance** | Legal requirements, reviews, audits | Compliance register, audit reports |

## 6. ISO 27017 (Cloud Security)

### 6.1 Cloud-Specific Controls

| Control | Implementation | Evidence |
|---------|----------------|----------|
| **Shared Responsibility Model** | Clear delineation of responsibilities | Responsibility matrix, customer agreements |
| **Cloud Service Customer Assets** | Asset protection, isolation, portability | Isolation controls, export capabilities |
| **Virtual Machine Hardening** | Secure configurations, patching | Hardening standards, patch logs |
| **Virtual Network Security** | Network segmentation, monitoring | Network policies, traffic logs |
| **Cloud Service Monitoring** | Comprehensive monitoring, alerting | Monitoring dashboards, alert logs |
| **Virtual Environment Isolation** | Strong isolation between tenants | Isolation testing, audit reports |

## 7. ISO 27018 (Cloud Privacy)

### 7.1 Privacy Controls

| Control | Implementation | Evidence |
|---------|----------------|----------|
| **Consent** | Explicit consent mechanisms | Consent records, opt-in logs |
| **Purpose Specification** | Clear purpose statements | Privacy notices, processing records |
| **Collection Limitation** | Minimal data collection | Data minimization policies |
| **Data Subject Rights** | Rights management system | Request logs, response records |
| **Transparency** | Clear privacy information | Privacy policy, transparency reports |
| **Communication** | Privacy breach notification | Notification procedures, breach logs |

## 8. SOC 2 Type II

### 8.1 Trust Services Criteria

| Criterion | Implementation | Evidence |
|-----------|----------------|----------|
| **Security** | Comprehensive security controls | Security architecture, control testing |
| **Availability** | High availability design, monitoring | Uptime reports, incident logs |
| **Processing Integrity** | Data validation, error handling | Processing logs, quality metrics |
| **Confidentiality** | Encryption, access controls | Encryption policies, access logs |
| **Privacy** | Privacy controls, GDPR compliance | Privacy assessments, compliance reports |

### 8.2 SOC 2 Audit Cycle

```
┌─────────────────────────────────────────────────────┐
│         SOC 2 Type II Audit Process                  │
├─────────────────────────────────────────────────────┤
│                                                       │
│  Planning Phase (Month 1-2)                          │
│  ├─ Scope definition                                 │
│  ├─ Control selection                                │
│  └─ Audit preparation                                │
│                                                       │
│  Observation Period (Month 3-8)                      │
│  ├─ Control operation (6 months minimum)             │
│  ├─ Evidence collection                              │
│  └─ Continuous monitoring                            │
│                                                       │
│  Testing Phase (Month 9-10)                          │
│  ├─ Control testing                                  │
│  ├─ Evidence review                                  │
│  └─ Findings documentation                           │
│                                                       │
│  Reporting Phase (Month 11-12)                       │
│  ├─ Report preparation                               │
│  ├─ Management review                                │
│  └─ Report issuance                                  │
│                                                       │
└─────────────────────────────────────────────────────┘
```

## 9. Compliance Monitoring & Reporting

### 9.1 Continuous Compliance Monitoring

```
┌─────────────────────────────────────────────────────┐
│         Compliance Monitoring Architecture           │
├─────────────────────────────────────────────────────┤
│                                                       │
│  ┌──────────────┐  ┌──────────────┐  ┌───────────┐ │
│  │  Automated   │  │   Manual     │  │  External │ │
│  │   Scanning   │  │   Reviews    │  │   Audits  │ │
│  │              │  │              │  │           │ │
│  │ • Daily      │  │ • Monthly    │  │ • Annual  │ │
│  │ • Real-time  │  │ • Quarterly  │  │ • Ad-hoc  │ │
│  │ • Continuous │  │ • Risk-based │  │ • Cert    │ │
│  └──────────────┘  └──────────────┘  └───────────┘ │
│                                                       │
│  ┌──────────────────────────────────────────────┐  │
│  │         Compliance Dashboard                  │  │
│  │  • Real-time compliance status                │  │
│  │  • Control effectiveness metrics              │  │
│  │  • Remediation tracking                       │  │
│  │  • Audit readiness score                      │  │
│  └──────────────────────────────────────────────┘  │
│                                                       │
└─────────────────────────────────────────────────────┘
```

### 9.2 Compliance Reporting Schedule

| Report Type | Frequency | Audience | Content |
|-------------|-----------|----------|---------|
| **Compliance Dashboard** | Real-time | Internal | Current compliance status, metrics |
| **Control Testing Report** | Monthly | Management | Control effectiveness, findings |
| **Compliance Summary** | Quarterly | Board | High-level compliance status |
| **Audit Report** | Annual | External | Third-party audit results |
| **Certification Report** | Annual | Customers | Certification status, scope |
| **Incident Report** | As needed | Regulators | Security incidents, breaches |
| **Transparency Report** | Annual | Public | Requests, compliance statistics |

## 10. Compliance Attestations & Certifications

### 10.1 Current Certifications

```
┌─────────────────────────────────────────────────────┐
│         Certification Status                         │
├─────────────────────────────────────────────────────┤
│                                                       │
│  ✓ ISO 27001:2022 - Information Security            │
│    Issued: 2024-01-15 | Valid until: 2027-01-15    │
│                                                       │
│  ✓ ISO 27017:2015 - Cloud Security                  │
│    Issued: 2024-01-15 | Valid until: 2027-01-15    │
│                                                       │
│  ✓ ISO 27018:2019 - Cloud Privacy                   │
│    Issued: 2024-01-15 | Valid until: 2027-01-15    │
│                                                       │
│  ✓ SOC 2 Type II                                     │
│    Period: 2023-06-01 to 2024-05-31                 │
│                                                       │
│  ✓ CSA STAR Level 2                                  │
│    Issued: 2024-02-01 | Valid until: 2026-02-01    │
│                                                       │
│  ✓ EU Cloud Code of Conduct                         │
│    Issued: 2024-03-01 | Valid until: 2026-03-01    │
│                                                       │
└─────────────────────────────────────────────────────┘
```

### 10.2 Attestation of Compliance

**We hereby attest that the EU Sovereign Cloud:**

1. Complies with all applicable EU regulations (GDPR, NIS2, DORA, eIDAS)
2. Maintains ISO 27001, 27017, and 27018 certifications
3. Achieves SOC 2 Type II compliance annually
4. Implements industry best practices and standards
5. Undergoes regular third-party audits and assessments
6. Maintains comprehensive documentation and evidence
7. Provides transparency through regular reporting
8. Ensures data sovereignty within EU borders
9. Protects customer data with appropriate controls
10. Continuously monitors and improves compliance posture

## Related Documents
- [Security Framework](../security/SECURITY_FRAMEWORK.md)
- [Data Governance](../governance/DATA_GOVERNANCE.md)
- [Architecture Overview](../architecture/00_OVERVIEW.md)
- [Audit Reports](AUDIT_REPORTS.md)