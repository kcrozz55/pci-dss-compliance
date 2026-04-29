# PCI-DSS v4.0 Self-Assessment Questionnaire D (SAQ-D)

**Organization:** MedCore Health Systems
**Address:** 1200 Peachtree Street NE, Atlanta, GA 30309
**Assessment Date:** February 10–14, 2026
**SAQ Version:** PCI-DSS v4.0 SAQ D v4
**Merchant Level:** Level 4
**ISA / Assessor:** Carlos Vega, PCI-ISA
**Approved By:** Jonathan E. Steele, CISO
**Status:** COMPLIANT

---

## Part 1 — Assessor Information

| Item | Detail |
|------|--------|
| Company Name | MedCore Health Systems |
| Assessment Prepared By | Carlos Vega, IAM/Billing Security, PCI-ISA |
| Assessment Period | February 10–14, 2026 |
| Report Submission Date | February 28, 2026 |
| Payment Brands | Visa, Mastercard, Discover |
| Payment Channels | Card-present (POS terminals), Card-not-present (billing portal) |
| Tokenization Provider | Stripe (PCI-DSS Level 1 Certified Service Provider) |

---

## Part 2 — Executive Summary

MedCore Health Systems completed its annual PCI-DSS v4.0 SAQ-D self-assessment for the period February 10–14, 2026. The assessment covered all 12 PCI-DSS requirements and applicable sub-requirements for the MedCore Cardholder Data Environment (CDE).

MedCore leverages Stripe as its PCI-DSS Level 1 certified payment processor. All primary account numbers (PANs) are tokenized at point of entry by Stripe and are never stored, processed, or transmitted through MedCore's own systems in cleartext. This significantly reduces the CDE scope to network controls, access controls, and monitoring requirements.

**Overall Assessment Conclusion: COMPLIANT**

| Requirement Area | Finding |
|-----------------|---------|
| Requirements 1-6 (Build and Maintain Secure Network/Systems) | Compliant |
| Requirements 7-9 (Protect Cardholder Data / Access) | Compliant |
| Requirements 10-11 (Monitor and Test Networks) | Compliant |
| Requirement 12 (Information Security Policy) | Compliant |

---

## Requirement 1 — Install and Maintain Network Security Controls

### 1.1 — Processes and mechanisms for network security controls
**Status:** In Place
**Evidence:** Network security policy documented in ISP-001 and POL-AC-001; firewall rule review conducted January 2026

### 1.2 — Network security controls between network segments
**Status:** In Place
**Evidence:** CDE isolated on VLAN 120; firewall rules restrict inbound/outbound traffic; only required ports/protocols permitted (HTTPS/443, payment gateway API); rule set reviewed semi-annually

### 1.3 — Network access to and from the CDE is restricted
**Status:** In Place
**Evidence:** WAF deployed in front of billing portal (AWS WAF + OWASP Core Rule Set v3.3); network diagram reviewed and current; no direct connections between internet and CDE

### 1.4 — Network connections between trusted and untrusted networks controlled
**Status:** In Place
**Evidence:** DMZ zone separates internet from application tier; all payment traffic routed through WAF and load balancer; FW_DMZ and FW_APP firewall rules documented

### 1.5 — Risks to CDE from computing devices capable of connecting to both untrusted networks and CDE
**Status:** In Place
**Evidence:** Mobile device policy enforced; CrowdStrike Falcon on all endpoints connecting to CDE; split-tunneling disabled on VPN

---

## Requirement 2 — Apply Secure Configurations to All System Components

### 2.1 — Processes and mechanisms for secure configurations
**Status:** In Place
**Evidence:** CIS Benchmark hardening applied to all CDE systems; baseline configurations documented in CMDB; configuration management policy (POL-CM-003) implemented

### 2.2 — System components are configured and managed securely
**Status:** In Place
**Evidence:** All CDE systems hardened per CIS Level 2 benchmarks; vendor default passwords changed; unnecessary services disabled; quarterly configuration compliance scanning via AWS Config and Qualys

### 2.3 — Wireless environments are configured and managed securely
**Status:** In Place
**Evidence:** Clinic Wi-Fi networks segmented; payment network on isolated SSID with WPA3-Enterprise; guest wireless not connected to CDE

---

## Requirement 3 — Protect Stored Account Data

### 3.1 — Processes and mechanisms for protecting stored account data
**Status:** In Place / Not Applicable (PAN Storage)
**Evidence:** MedCore does not store Primary Account Numbers (PANs). Stripe tokenizes all PANs at point of entry. Token values are stored in MedCore's billing system; tokens cannot be reverse-engineered to recover PANs. Data retention policy (6 years for billing records) applies to tokens only.

### 3.2 — Account data storage is kept to a minimum
**Status:** In Place
**Evidence:** No cardholder data other than truncated PAN (last 4 digits) retained in receipts. Data flow diagram confirms no full PAN storage anywhere in MedCore systems.

### 3.3 — Sensitive authentication data (SAD) is not stored after authorization
**Status:** In Place / Not Applicable
**Evidence:** Stripe handles all authorization processing. MedCore never receives, transmits, or stores CVV2, CVC2, or magnetic stripe data.

### 3.4 — Primary Account Numbers are protected wherever stored
**Status:** In Place / Not Applicable
**Evidence:** No PANs stored. Stripe tokens stored in billing database (AES-256 encrypted at rest via AWS RDS encryption).

---

## Requirement 4 — Protect Cardholder Data with Strong Cryptography During Transmission

### 4.1 — Processes and mechanisms for protecting cardholder data during transmission
**Status:** In Place
**Evidence:** Encryption policy POL-SC-002; TLS 1.3 enforced on all payment-related web endpoints; TLS 1.0 and 1.1 disabled; verified via SSL Labs scan (December 2025 — Grade A+)

### 4.2 — PANs are protected with strong cryptography during transmission
**Status:** In Place / Not Applicable
**Evidence:** Payment data transmitted directly from browser to Stripe using Stripe.js (PCI P2PE equivalent); MedCore backend never receives cleartext PANs; all API calls to Stripe use TLS 1.3 with certificate pinning

---

## Requirement 5 — Protect All Systems Against Malicious Software

### 5.1 — Processes and mechanisms for protecting against malicious software
**Status:** In Place
**Evidence:** Anti-malware policy documented; CrowdStrike Falcon Prevent + Detect deployed on all CDE systems; definitions updated automatically; real-time scanning enabled

### 5.2 — Malicious software is prevented and detected
**Status:** In Place
**Evidence:** CrowdStrike EDR active on all CDE servers and workstations; email filtering (Proofpoint) blocks malicious attachments; phishing simulation rate 3.2% (target < 5%)

### 5.3 — Anti-malware mechanisms are maintained
**Status:** In Place
**Evidence:** CrowdStrike sensor version current; signature updates automated; monthly EDR health report reviewed by ISSO

---

## Requirement 6 — Develop and Maintain Secure Systems and Software

### 6.1 — Processes and mechanisms for developing and maintaining secure systems
**Status:** In Place
**Evidence:** Secure SDLC policy implemented; security review gates in JIRA project templates; OWASP Top 10 training for developers

### 6.2 — Bespoke and custom software is developed securely
**Status:** In Place
**Evidence:** SAST (SonarQube) integrated in CI/CD pipeline; DAST (OWASP ZAP) in QA environment; code review required for all changes; no critical/high vulnerabilities in production billing code

### 6.3 — Security vulnerabilities are identified and addressed
**Status:** In Place
**Evidence:** Qualys vulnerability management program; daily scanning; critical/high CVEs in CDE patched within 15/30 days respectively; CVSS scoring used for prioritization

### 6.4 — Public-facing web applications are protected against attacks
**Status:** In Place
**Evidence:** AWS WAF deployed in front of billing portal; OWASP Core Rule Set v3.3; rate limiting enabled; SQL injection and XSS protection active; last web application penetration test November 2025 — no critical findings

---

## Requirement 7 — Restrict Access to System Components and Cardholder Data by Business Need to Know

### 7.1 — Processes and mechanisms for restricting access
**Status:** In Place
**Evidence:** Access Control Policy POL-AC-001; RBAC implemented; access requests require manager and ISSO approval via ServiceNow

### 7.2 — Access to system components and data is appropriately defined and assigned
**Status:** In Place
**Evidence:** CDE access limited to 12 users (billing staff + IT administrators); roles defined in Okta; quarterly access reviews conducted; last review completed January 2026

### 7.3 — Access to system components and data is managed via an access control system
**Status:** In Place
**Evidence:** Okta SSO enforces RBAC for billing portal; CyberArk manages privileged admin access to CDE servers; all access logged

---

## Requirement 8 — Identify Users and Authenticate Access to System Components

### 8.1 — Processes and mechanisms for user identification and authentication
**Status:** In Place
**Evidence:** IAM Policy implemented; unique user IDs required; shared accounts prohibited in CDE

### 8.2 — User identification and related accounts are managed
**Status:** In Place
**Evidence:** Onboarding/offboarding procedures; accounts disabled within 4 hours of termination; inactive accounts reviewed monthly

### 8.3 — User authentication is established via passwords/phrases or other authentication factors
**Status:** In Place
**Evidence:** Password policy: 16 characters minimum, complexity requirements, no reuse of last 24; MFA required for all CDE access via Okta; hardware tokens for privileged accounts

### 8.4 — Multi-factor authentication is implemented for all access into the CDE
**Status:** In Place
**Evidence:** MFA enforced via Okta for 100% of CDE user accounts; bypass procedures require CISO approval and are logged

---

## Requirement 9 — Restrict Physical Access to Cardholder Data

### 9.1 — Processes and mechanisms for restricting physical access
**Status:** In Place
**Evidence:** Physical security policy; badge access control; visitor logs maintained

### 9.2 — Physical access controls manage entry into facilities and systems
**Status:** In Place
**Evidence:** Biometric + badge access to server room housing CDE components; CCTV at all access points; access reviewed quarterly

### 9.3 — Physical access for personnel and visitors is authorized and managed
**Status:** In Place
**Evidence:** Visitor escort policy; visitor log with sign-in/out times; temporary badges expire after 8 hours

### 9.4 — Media with cardholder data is secured
**Status:** In Place / Not Applicable
**Evidence:** No PHI or card data on physical media. USB disabled via GPO. POS terminal hard drives encrypted. Decommissioned terminals follow NIST 800-88 disposal procedures.

### 9.5 — Point of interaction (POI) devices are protected from tampering and substitution
**Status:** In Place
**Evidence:** 6 POS terminals inventoried and tamper-evident seals inspected monthly by clinic managers; terminal serial numbers logged; staff trained on skimming device detection

---

## Requirement 10 — Log and Monitor All Access to System Components and Cardholder Data

### 10.1 — Processes and mechanisms for logging and monitoring
**Status:** In Place
**Evidence:** Audit logging policy; all CDE access events logged to Splunk SIEM; logs immutable (S3 Object Lock)

### 10.2 — Audit logs capture all individual user access to cardholder data
**Status:** In Place
**Evidence:** All billing portal logins, token retrievals, and billing record access logged with user ID, timestamp, and source IP; logs retained 12 months online, 24 months archived

### 10.3 — Audit logs are protected from destruction and unauthorized modifications
**Status:** In Place
**Evidence:** Logs written to immutable S3 bucket with Object Lock (WORM); log integrity monitored; SIEM admin access restricted to 3 personnel

### 10.4 — Audit logs are reviewed to identify anomalies or suspicious activity
**Status:** In Place
**Evidence:** Splunk correlation rules alert on anomalous access patterns; SIEM reviewed daily by on-call security analyst; weekly SIEM summary to CISO

---

## Requirement 11 — Test Security of Systems and Networks Regularly

### 11.1 — Processes for testing security
**Status:** In Place
**Evidence:** Security testing policy; annual penetration test; quarterly ASV scan; internal vulnerability scanning weekly

### 11.2 — Wireless access points are identified and monitored
**Status:** In Place
**Evidence:** Wireless access point inventory maintained; rogue AP detection enabled via wireless IDS; no unauthorized APs detected in last 12 months

### 11.3 — External and internal vulnerabilities are regularly identified, prioritized, and addressed
**Status:** In Place
**Evidence:** Qualys WAS quarterly external scans (ASV-qualified); internal scans weekly; last quarterly scan March 2026 — no CDE critical findings

### 11.4 — External and internal penetration testing is regularly performed
**Status:** In Place
**Evidence:** Annual penetration test conducted by Optiv Security (November 2025); CDE in scope; no critical findings; two medium findings remediated by January 2026

### 11.5 — Network intrusions and unexpected file changes are detected and responded to
**Status:** In Place
**Evidence:** CrowdStrike EDR provides file integrity monitoring on CDE servers; SIEM alerts on unexpected changes to critical files; IDS (AWS GuardDuty) active on CDE network

---

## Requirement 12 — Support Information Security with Organizational Policies and Programs

### 12.1 — Comprehensive information security policy is established, published, maintained, and disseminated
**Status:** In Place
**Evidence:** Information Security Policy ISP-001 approved by CEO; distributed to all staff annually; available on intranet; reviewed January 2026

### 12.2 — Acceptable use policies are implemented for end-user technologies
**Status:** In Place
**Evidence:** Acceptable Use Policy POL-AU-002; signed at onboarding; annual re-acknowledgment required

### 12.3 — Risks to cardholder data and the CDE are identified, evaluated, and managed
**Status:** In Place
**Evidence:** Annual risk assessment per ISO 27005; PCI-specific risk register maintained; all identified CDE risks have assigned owners and treatment plans

### 12.4 — PCI-DSS compliance is managed throughout the year
**Status:** In Place
**Evidence:** Quarterly compliance reviews; CISO presents PCI status to executive leadership; compliance calendar maintained and tracked

### 12.5 — The PCI-DSS scope is documented and validated
**Status:** In Place
**Evidence:** Annual scope confirmation; network segmentation test completed February 2026; data flow diagram current and reflects tokenization architecture

### 12.6 — Security awareness education is provided
**Status:** In Place
**Evidence:** Annual PCI security awareness training for all billing and CDE-adjacent staff; KnowBe4 PCI module completed by 100% of relevant staff as of January 2026

### 12.7 — Personnel with access to cardholder data are screened
**Status:** In Place
**Evidence:** Background checks required for all billing staff per HR policy; rescreening every 3 years

### 12.8 — Risks associated with third-party/vendor relationships are managed
**Status:** In Place
**Evidence:** Stripe maintained as PCI-DSS Level 1 Service Provider; annual review of Stripe Attestation of Compliance (AoC); contractual security requirements in vendor agreements

### 12.9 — Third-party service providers (TPSPs) support customers compliance
**Status:** In Place
**Evidence:** Stripe provides annual AoC; MedCore maintains copy in vendor risk register; Stripe's PCI responsibility matrix reviewed

### 12.10 — Suspected and confirmed security incidents are responded to immediately
**Status:** In Place
**Evidence:** Incident Response Policy POL-IR-001; PCI breach notification procedures documented; payment brand contacts (Visa, Mastercard) maintained in IR plan; tabletop exercise conducted Q3 2025

---

## Attestation of Compliance

I confirm that the information provided in this Self-Assessment Questionnaire is accurate and complete, and that MedCore Health Systems is in compliance with PCI-DSS v4.0 requirements as assessed during the period February 10-14, 2026.

| Role | Name | Date |
|------|------|------|
| PCI-ISA / Assessor | Carlos Vega | February 28, 2026 |
| CISO | Jonathan E. Steele | February 28, 2026 |
| CEO (Executive Sponsor) | Marcus T. Hargrove | February 28, 2026 |
