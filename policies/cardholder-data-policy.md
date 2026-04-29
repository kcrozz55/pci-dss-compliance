# Cardholder Data Handling Policy

**Policy ID:** POL-PCI-001
**Version:** 2.1
**Classification:** Internal
**Owner:** Jonathan E. Steele, CISO
**Approved By:** Marcus T. Hargrove, CEO
**Effective Date:** January 1, 2026
**Review Date:** January 1, 2027
**Regulatory Basis:** PCI-DSS v4.0 Requirements 3, 4, 7, 12

---

## 1. Purpose

This policy establishes the requirements for handling, protecting, and managing payment cardholder data at MedCore Health Systems. It ensures compliance with the Payment Card Industry Data Security Standard (PCI-DSS) v4.0 and protects patients from financial harm resulting from payment data exposure.

---

## 2. Scope

This policy applies to all MedCore employees, contractors, and third-party vendors who:
- Accept, process, or handle payment card transactions
- Access systems that support payment processing
- Have any role in the Cardholder Data Environment (CDE)

---

## 3. Cardholder Data Handling Requirements

### 3.1 Data Minimization

MedCore shall collect the minimum cardholder data necessary to complete a payment transaction. The following data elements govern acceptable retention:

| Data Element | Permissible to Store? | Conditions |
|-------------|----------------------|------------|
| Primary Account Number (PAN) | NO | PANs must never be stored in MedCore systems; Stripe tokenization is mandatory |
| Cardholder Name | Only if required by business | Must be masked when displayed |
| Expiration Date | Only if required by business | Must not be stored with full PAN |
| Service Code | NO | Not required for any MedCore business process |
| CVV2 / CVC2 / CAV2 | NEVER | Must not be stored under any circumstances, even if encrypted |
| PIN / PIN Block | NEVER | MedCore does not process PIN transactions |
| Magnetic Stripe Data | NEVER | Full track data must never be stored |
| Stripe Token | YES | Tokens are not cardholder data; stored in billing system for recurring payments |
| Last 4 Digits of PAN | YES | For display on receipts only; cannot be used to reconstruct PAN |

### 3.2 Tokenization Requirement

All payment card transactions must be processed using Stripe's payment tokenization service:

- Patients enter card details directly into Stripe-hosted payment fields (Stripe.js / Elements)
- Card data is encrypted client-side by Stripe before transmission
- MedCore backend systems receive only a Stripe token; they never receive the PAN
- POS terminals use Stripe Terminal devices with end-to-end encryption
- Bypassing Stripe tokenization to capture PANs directly is prohibited and constitutes a policy violation

### 3.3 Transmission Security

All cardholder data in transit must be protected:
- TLS 1.3 is required for all payment-related web communications
- TLS 1.0 and 1.1 are disabled on all CDE systems
- Unencrypted transmission of cardholder data over any network is prohibited
- Email transmission of card numbers is prohibited under all circumstances

---

## 4. Access Control Requirements

- Access to CDE systems requires MFA via Okta
- Access is granted based on business need to know; default is no access
- Access must be formally requested, approved by manager and ISSO, and provisioned via ServiceNow
- CDE access is reviewed quarterly; unnecessary access is revoked
- Shared or generic accounts in the CDE are prohibited
- Admin access requires CyberArk PAM with session recording

---

## 5. Physical Handling of Payment Cards

- Staff must never write down, photograph, or manually record card numbers
- POS terminal screens must be positioned to prevent shoulder surfing
- POS devices must be inspected for tampering at the start of each shift
- Suspicious POS devices must be reported immediately to IT Security (ext. 2500)
- Patients requesting written receipts receive truncated receipts (last 4 digits only)

---

## 6. Incident Reporting

Any suspected or confirmed compromise of cardholder data must be reported immediately:

1. Contact IT Security: security@medcorehealthsystems.com or emergency line 1-800-555-0199
2. Do not attempt to investigate independently
3. Preserve all logs and system states
4. Do not notify the cardholder without CISO and Legal approval
5. CISO will notify payment brands (Visa/Mastercard) and acquiring bank within required timeframes

---

## 7. Violations

Violations of this policy may result in:
- Disciplinary action up to and including termination
- Personal liability for regulatory fines
- Notification to law enforcement
- PCI-DSS compliance suspension and loss of card acceptance privileges

---

## 8. Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | January 2024 | J. Steele | Initial release |
| 2.0 | January 2025 | J. Steele | Updated for PCI-DSS v4.0 |
| 2.1 | January 2026 | A. Osei | Annual review; Stripe Terminal language added |
