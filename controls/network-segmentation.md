# PCI-DSS CDE Network Segmentation Documentation

**Document ID:** PCI-NET-001
**Version:** 2.0
**Classification:** Confidential
**Owner:** Patrick O. Sullivan, Network Engineer
**Reviewed By:** Jonathan E. Steele, CISO
**Last Validated:** February 2026 (Annual Segmentation Test)
**Next Validation:** February 2027

---

## 1. Purpose

This document describes the network segmentation controls that isolate MedCore's Cardholder Data Environment (CDE) from all other network segments. Proper segmentation reduces PCI-DSS scope and protects payment processing systems from lateral movement threats.

---

## 2. CDE Boundary Definition

The CDE consists of all systems that store, process, or transmit cardholder data, or that could impact the security of cardholder data. Given MedCore's tokenization architecture with Stripe, the CDE scope is limited to:

| System | CDE Classification | Justification |
|--------|-------------------|---------------|
| Patient Billing Portal (AWS) | CDE-adjacent | Initiates payment flows; transmits token requests to Stripe |
| Billing Application Server (AWS) | CDE-adjacent | Stores tokenized payment references; no PANs |
| POS Terminals (6 terminals at clinics) | CDE in-scope | Card-present transactions at point of interaction |
| CDE VLAN (VLAN 120) | CDE network | Isolated payment network segment |
| Stripe (Payment Processor) | Service Provider CDE | Processes, stores, and transmits actual PANs |

---

## 3. Network Zones and Segmentation Architecture

MedCore's network architecture uses a six-zone segmentation model as documented in the EHRP System Architecture:

| Zone | VLAN | Purpose | CDE? |
|------|------|---------|------|
| PUBLIC_ZONE | N/A | Public internet-facing perimeter | No |
| DMZ_ZONE | VLAN 10 | WAF, Load Balancer, Firewall | No |
| APP_ZONE | VLAN 20 | Application servers, API, Auth | No |
| DATA_ZONE | VLAN 30 | Databases, caching | No |
| MGMT_ZONE | VLAN 40 | SIEM, bastion host, admin | No |
| INTEGRATION_ZONE | VLAN 50 | HL7, HIE gateway | No |
| CDE_ZONE | VLAN 120 | POS terminals, billing portal | YES |

---

## 4. CDE Firewall Rules

The following firewall rules control traffic to and from the CDE VLAN (VLAN 120):

### Inbound to CDE (VLAN 120)

| Rule ID | Source | Destination | Port/Protocol | Action | Purpose |
|---------|--------|-------------|---------------|--------|---------|
| FW-CDE-IN-001 | DMZ_ZONE (VLAN 10) | VLAN 120 (Billing Portal) | TCP/443 (HTTPS) | ALLOW | Patient payment web traffic via WAF |
| FW-CDE-IN-002 | MGMT_ZONE (VLAN 40) | VLAN 120 | TCP/22 (SSH) | ALLOW | Admin access via bastion host only |
| FW-CDE-IN-003 | MGMT_ZONE (VLAN 40) | VLAN 120 | TCP/443 (HTTPS) | ALLOW | CrowdStrike sensor management |
| FW-CDE-IN-099 | ANY | VLAN 120 | ANY | DENY | Implicit deny all other inbound |

### Outbound from CDE (VLAN 120)

| Rule ID | Source | Destination | Port/Protocol | Action | Purpose |
|---------|--------|-------------|---------------|--------|---------|
| FW-CDE-OUT-001 | VLAN 120 | api.stripe.com | TCP/443 (HTTPS) | ALLOW | Tokenization API calls to Stripe |
| FW-CDE-OUT-002 | VLAN 120 | DATA_ZONE (VLAN 30) | TCP/5432 (PostgreSQL) | ALLOW | Billing database queries (token storage) |
| FW-CDE-OUT-003 | VLAN 120 | MGMT_ZONE (VLAN 40) | TCP/514, TCP/9997 (Syslog/Splunk) | ALLOW | Log forwarding to SIEM |
| FW-CDE-OUT-004 | VLAN 120 | AWS KMS endpoint | TCP/443 (HTTPS) | ALLOW | Encryption key operations |
| FW-CDE-OUT-099 | VLAN 120 | ANY | ANY | DENY | Implicit deny all other outbound |

---

## 5. POS Terminal Network Segmentation

On-premises POS terminals at each clinic location are isolated on a dedicated VLAN segment:

| Clinic | VLAN | Segment | Firewall | VPN Tunnel |
|--------|------|---------|---------|-----------|
| Clinic 1 — Midtown Atlanta | VLAN 120 | 10.120.1.0/27 | FW_CDE_C1 | IPSec to AWS GovCloud |
| Clinic 2 — Buckhead | VLAN 120 | 10.120.2.0/27 | FW_CDE_C2 | IPSec to AWS GovCloud |
| Clinic 3 — Decatur | VLAN 120 | 10.120.3.0/27 | FW_CDE_C3 | IPSec to AWS GovCloud |
| Clinic 4 — Sandy Springs | VLAN 120 | 10.120.4.0/27 | FW_CDE_C4 | IPSec to AWS GovCloud |

**POS terminal rules:**
- POS terminals may only communicate with Stripe payment gateway over TLS 1.3
- POS terminals are prohibited from accessing the MedCore application network (APP_ZONE)
- POS terminal management (firmware updates) via dedicated management VLAN with MFA-required admin access
- All POS terminal traffic logged to SIEM

---

## 6. Segmentation Test Results — February 2026

An annual network segmentation test was conducted February 12, 2026 by Carlos Vega (PCI-ISA) to validate that all in-scope systems are isolated from out-of-scope systems.

### Test Methodology

Network penetration testing was conducted from multiple external and internal positions to verify:
1. No unauthorized paths from out-of-CDE segments to CDE VLAN 120
2. No unauthorized paths from CDE VLAN 120 to non-permitted destinations
3. Firewall rules enforce documented allow/deny policy
4. POS terminal segments are isolated from corporate network

### Test Results

| Test Case | From | To | Expected | Result |
|-----------|------|----|---------|--------|
| ST-001 | Public Internet | VLAN 120 direct | DENY | PASS |
| ST-002 | APP_ZONE (VLAN 20) | VLAN 120 | DENY | PASS |
| ST-003 | DATA_ZONE (VLAN 30) | VLAN 120 | DENY | PASS |
| ST-004 | VLAN 120 | APP_ZONE (VLAN 20) | DENY | PASS |
| ST-005 | VLAN 120 | api.stripe.com (TCP/443) | ALLOW | PASS |
| ST-006 | VLAN 120 | Any other internet destination | DENY | PASS |
| ST-007 | Clinic VLAN 120 | MedCore Corporate LAN | DENY | PASS |
| ST-008 | Clinic VLAN 120 | Stripe (TLS/443) | ALLOW | PASS |
| ST-009 | MGMT_ZONE | VLAN 120 (SSH via bastion) | ALLOW | PASS |
| ST-010 | VLAN 120 | SIEM (TCP/9997) | ALLOW | PASS |

**All 10 segmentation test cases PASSED.**

**Conclusion:** The CDE is properly isolated. Network segmentation controls are effective and reduce PCI-DSS scope as intended.

---

## 7. Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | March 2025 | P. Sullivan | Initial documentation |
| 2.0 | February 2026 | P. Sullivan | Annual update; test results added; VLAN diagram updated |
