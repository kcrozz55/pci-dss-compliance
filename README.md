# PCI-DSS v4.0 Compliance Program

**Organization:** MedCore Health Systems
**Standard:** Payment Card Industry Data Security Standard (PCI-DSS) v4.0
**Scope:** Cardholder Data Environment (CDE) — Patient Billing and Payment Processing
**Merchant Level:** Level 4 (Visa/MC — fewer than 20,000 e-commerce transactions annually)
**SAQ Type:** SAQ D — Merchants
**Status:** Compliant | Last Assessment: Q1 2026
**Last Updated:** April 2026

---

## Overview

MedCore Health Systems processes payment card transactions for patient copays, self-pay balances, and administrative fees. While MedCore's primary compliance obligation is HIPAA for Protected Health Information, the organization also maintains PCI-DSS compliance to protect cardholder data processed through the patient billing portal.

This repository documents MedCore's PCI-DSS v4.0 compliance program, including the Self-Assessment Questionnaire (SAQ-D), network segmentation validation, control implementation evidence, and annual review records.

---

## Cardholder Data Environment (CDE) Scope

| Component | Description | Location |
|-----------|-------------|----------|
| Patient Billing Portal | Web application accepting card payments | AWS GovCloud |
| Payment Gateway | Stripe (PCI-DSS Level 1 certified service provider) | Cloud (SaaS) |
| Point-of-Sale Terminals | 6 countertop terminals across 4 clinic locations | On-Premises |
| CDE Network Segment | Isolated VLAN (VLAN 120) for payment processing | On-Premises / AWS |
| Billing Application Server | Patient accounts and billing records | AWS GovCloud |

**Note:** MedCore leverages Stripe as a PCI-DSS Level 1 certified payment processor. Card numbers are tokenized at point of entry and never stored in MedCore systems. This significantly reduces CDE scope.

---

## PCI-DSS v4.0 Requirements Summary

| Requirement | Title | Status |
|-------------|-------|--------|
| 1 | Install and Maintain Network Security Controls | Compliant |
| 2 | Apply Secure Configurations to All System Components | Compliant |
| 3 | Protect Stored Account Data | Compliant (No PANs stored) |
| 4 | Protect Cardholder Data with Strong Cryptography During Transmission | Compliant |
| 5 | Protect All Systems and Networks from Malicious Software | Compliant |
| 6 | Develop and Maintain Secure Systems and Software | Compliant |
| 7 | Restrict Access to System Components and Cardholder Data | Compliant |
| 8 | Identify Users and Authenticate Access to System Components | Compliant |
| 9 | Restrict Physical Access to Cardholder Data | Compliant |
| 10 | Log and Monitor All Access to System Components and Cardholder Data | Compliant |
| 11 | Test Security of Systems and Networks Regularly | Compliant |
| 12 | Support Information Security with Organizational Policies and Programs | Compliant |

---

## Repository Structure

```
pci-dss-compliance/
├── README.md                              # This file
├── saq/
│   └── saq-d-self-assessment.md          # SAQ-D completed assessment
├── controls/
│   ├── network-segmentation.md           # CDE network segmentation documentation
│   └── control-evidence-summary.md       # Evidence summary for all 12 requirements
└── policies/
    └── cardholder-data-policy.md         # Cardholder data handling policy
```

---

## Key Personnel

| Role | Name |
|------|------|
| PCI Compliance Owner | Jonathan E. Steele, CISO |
| Billing Systems Owner | Dr. Priya Nambiar, CIO |
| PCI Internal Security Assessor (ISA) | Carlos Vega, IAM / Billing Security |
| Privacy Officer | Dr. Rachel Feinberg |

---

## Compliance Calendar

| Activity | Frequency | Last Completed | Next Due |
|----------|-----------|---------------|---------|
| SAQ-D Self-Assessment | Annual | February 2026 | February 2027 |
| Internal Vulnerability Scan (ASV) | Quarterly | March 2026 | June 2026 |
| External Penetration Test | Annual | November 2025 | November 2026 |
| Network Segmentation Test | Annual | February 2026 | February 2027 |
| Security Awareness Training (payment staff) | Annual | January 2026 | January 2027 |
| Firewall Rule Review | Semi-annual | January 2026 | July 2026 |

---

## Frameworks Referenced

- PCI Security Standards Council — PCI-DSS v4.0 (March 2022)
- PCI SSC — Self-Assessment Questionnaire D v4.0
- NIST SP 800-53 Rev 5 (cross-referenced for control alignment)
- HIPAA Security Rule (45 CFR Part 164) — dual-framework alignment
