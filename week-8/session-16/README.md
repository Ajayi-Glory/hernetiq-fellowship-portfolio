# CartBot AI Security Assessment — Session 16

## 1. Overview

This assessment evaluates the security of a simulated AI e-commerce application, CartBot. The goal was to move from identifying vulnerabilities to understanding their impact, applying realistic remediation, and verifying that the fixes worked.

The assessment followed this workflow:

**Threat Model → Security Questions → Vulnerability Discovery → Impact Validation → Finding → Remediation → Verification**

---

## 2. Threat Model

### CartBot Components

The main components of the CartBot system are:

- **Customer** — uses the CartBot application and requests information about products and orders.
- **Seller** — manages product information that can enter the AI application's context.
- **CartBot API/Application** — handles authentication, customer requests, product information, and order data.
- **Product Data** — contains product information used by the application and AI assistant.
- **Customer/Order Data** — contains sensitive customer and order information.
- **AI Assistant/Model** — processes user requests and uses application data to generate responses.

### Important Data Flows

1. Customer sends a request to the CartBot API.
2. The API processes authentication and authorization.
3. The application accesses product or customer/order data.
4. Relevant information can be provided to the AI assistant.
5. The AI assistant generates a response for the customer.

---

## 3. Trust Boundaries

Important trust boundaries include:

### Customer → CartBot API

Customer-controlled input must not automatically be trusted. Authentication and authorization must be validated by the server.

### Seller/Product Content → AI Assistant

Seller-controlled product content may enter the AI context. This content should be treated as untrusted data rather than trusted instructions.

### CartBot API → Customer/Order Data

The API must verify that an authenticated customer is authorized to access the requested customer or order information.

### CartBot API → AI Model

Information passed to the AI model should be controlled and protected against malicious or manipulated input.

---

## 4. Security Findings

### Finding 1 — Broken Authentication

**Observed behavior:** 
The vulnerable configuration had JWT validation disabled.

**Expected security behavior:** 
The API should validate authentication tokens on the server before allowing access to protected functionality.

**Root cause:** 
`REQUIRE_JWT_VALIDATION` was disabled.

**Impact:** 
An attacker could potentially access protected functionality without proper authentication.

**Severity:** High

**Remediation:** 
JWT validation was enabled and the JWT secret was moved to an environment variable.

---

### Finding 2 — Broken Object-Level Authorization (BOLA)

**Observed behavior:** 
The API trusted a customer ID supplied by the client.

**Expected security behavior:** 
The server should determine the authenticated customer's identity from a validated authentication token and verify authorization before returning customer or order information.

**Root cause:** 
The application trusted client-supplied customer identification.

**Impact:** 
An attacker could potentially manipulate the customer identifier and attempt to access another customer's order information.

**Severity:** High

**Remediation:** 
Server-side JWT validation was implemented and the authenticated customer identity is checked before customer/order data is accessed.

---

### Finding 3 — Missing Rate Limiting

**Observed behavior:** 
Rate limiting was disabled.

**Expected security behavior:** 
The API should restrict repeated requests to reduce abuse and excessive resource consumption.

**Root cause:** 
`RATE_LIMIT_ENABLED` was set to `False`.

**Impact:** 
Repeated API or AI requests could create operational and financial exposure.

**Severity:** Medium

**Remediation:** 
Rate limiting was enabled in the hardened configuration.

---

### Finding 4 — Indirect Prompt Injection Risk

**Observed behavior:** 
Seller-controlled product content could potentially reach the AI model's context.

**Expected security behavior:** 
External product content should be treated as untrusted data and should not be allowed to override system instructions.

**Impact:** 
Malicious product content could influence the AI assistant and potentially cause unintended behavior or disclosure of information.

**Severity:** High

**Remediation:** 
The AI system prompt was restricted so that external content is treated as untrusted data rather than trusted instructions.

---

## 5. Static Analysis

I used Semgrep to perform static analysis against the vulnerable API configuration.

The scan identified three security findings:

- Client-supplied customer ID trust
- Disabled JWT validation
- Disabled rate limiting

This provided automated evidence of the insecure patterns, while manual analysis was used to understand their security impact.

---

## 6. Remediation

The CartBot configuration was hardened by:

- Enabling JWT validation.
- Moving the JWT secret to an environment variable.
- Removing trust in client-supplied customer identity.
- Checking the authenticated customer before accessing order data.
- Enabling rate limiting.
- Restricting the AI system prompt.
- Adding security-focused dependencies.

The vulnerable configuration was preserved as evidence and the hardened configuration was added separately.

---

## 7. Verification Evidence

Before remediation, the CartBot security tests produced:

**4 tests run — 4 FAILED**

After remediation, the same security tests produced:

**4 tests run — 0 FAILED — 4 PASSED**

This confirms that the expected security checks passed after the remediation.

---

## 8. Evidence

### Vulnerable Configuration

The original `api_config.py` configuration demonstrates the insecure authentication, authorization, and rate-limiting patterns.

### Semgrep Results

`semgrep-results.json` contains the results of the static security analysis.

### Hardened Configuration

`api_config_hardened.py` contains the implemented security controls.

### Verification

The before-and-after test results demonstrate that the security checks changed from **4 failures to 4 passes**.

### GitHub Commit

Remediation commit:

`af6ffd3`

Commit message:

**Level 3 fix: JWT validation, rate limiting, and restricted system prompt**

---

## 9. Conclusion

This assessment reinforced that finding a vulnerability is only one part of security work.

A professional security assessment should explain:

- What is wrong.
- Why the weakness exists.
- What an attacker could do.
- Who or what could be affected.
- How the risk can be reduced.
- How the fix can be verified.

The CartBot investigation followed this complete workflow from threat modeling and vulnerability discovery through remediation and verification.
