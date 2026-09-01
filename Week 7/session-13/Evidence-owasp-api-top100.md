# Session 13 — Evidence A
## OWASP API Security Top 10

### Introduction

In this evidence, I reviewed the OWASP API Security Top 10 and explained the major API security risks in my own words.

The main thing I learned is that securing an API is not only about checking whether a user is logged in. The server must also verify what the user is allowed to access, what actions they can perform, and whether the data and requests can be trusted.

---

## API1:2023 — Broken Object Level Authorization (BOLA)

BOLA occurs when an authenticated user can access another user's object simply by changing an identifier in a request.

**Example:** 
A user is logged in as User 101 but changes:

`/api/users/101/orders`

to:

`/api/users/102/orders`

If the API returns User 102's orders, the server failed to check authorization.

**What should be verified:** 
The server should check whether the authenticated user is authorized to access that specific object.

**Impact:** 
Unauthorized access to sensitive user information.

---

## API2:2023 — Broken Authentication

Broken authentication happens when an API does not properly verify the identity of a user or the validity of authentication credentials.

**Example:** 
An API accepts an invalid password or an expired authentication token and still gives the user access.

**What should be verified:** 
The server should properly validate credentials, sessions, and authentication tokens.

**Impact:** 
An attacker could impersonate another user.

---

## API3:2023 — Broken Object Property Level Authorization

This occurs when a user can access or modify properties of an object that they should not be allowed to change.

**Example:** 
A normal user changes their profile request to include:

`"role": "admin"`

If the server accepts this without checking authorization, the user could increase their privileges.

**What should be verified:** 
The server should control which properties each user is allowed to view or modify.

**Impact:** 
Sensitive information could be exposed or important properties could be changed.

---

## API4:2023 — Unrestricted Resource Consumption

This happens when an API does not properly control how many resources a user can consume.

**Example:** 
A user sends a request asking for an extremely large number of records at once.

**What should be verified:** 
The API should enforce limits, pagination, quotas, and appropriate rate controls.

**Impact:** 
Excessive requests can affect availability and increase infrastructure costs.

---

## API5:2023 — Broken Function Level Authorization

This happens when a user can access a function that should only be available to a higher-privileged user.

**Example:** 
A normal employee accesses:

`/api/admin/delete-user`

even though only administrators should be able to perform that action.

**What should be verified:** 
The server should check whether the authenticated user has permission to perform the requested function.

**Impact:** 
Attackers may perform administrative or privileged actions.

---

## API6:2023 — Unrestricted Access to Sensitive Business Flows

This occurs when an important business process can be abused because the API does not have enough protection against automated or excessive use.

**Example:** 
An online store allows customers to purchase a limited product. An attacker uses automation to repeatedly purchase large quantities before normal customers can buy them.

**What should be verified:** 
The application should identify sensitive business processes and protect them against abuse.

**Impact:** 
Business operations, availability, fairness, or revenue can be affected.

---

## API7:2023 — Server-Side Request Forgery (SSRF)

SSRF occurs when an application makes a request to a location controlled by the user without properly restricting where the server can connect.

**Example:** 
An API accepts a URL from a user and fetches the content from that URL. If destinations are not restricted, an attacker may attempt to make the server access internal services.

**What should be verified:** 
The application should validate and restrict user-controlled destinations.

**Impact:** 
An attacker may use the server to access resources that should not be publicly reachable.

---

## API8:2023 — Security Misconfiguration

Security misconfiguration happens when an API or its supporting infrastructure is configured insecurely.

**Examples include:**

- Debug mode enabled in production
- Overly permissive CORS settings
- Default credentials
- Detailed error messages exposing sensitive information
- Unnecessary endpoints or services being exposed

**What should be verified:** 
Production systems should use secure configurations and should not expose unnecessary functionality.

**Impact:** 
Attackers may gain useful information or exploit insecure settings.

---

## API9:2023 — Improper Inventory Management

This occurs when an organization does not properly keep track of its APIs, versions, endpoints, or environments.

**Example:** 
A company releases API version 2 but leaves an old version running with weaker security controls.

**What should be verified:** 
The organization should maintain an accurate inventory of APIs, versions, endpoints, and deprecated services.

**Impact:** 
Attackers may discover and exploit forgotten or outdated APIs.

---

## API10:2023 — Unsafe Consumption of APIs

This happens when an application trusts information received from another API without properly validating it.

**Example:** 
An application receives data from a third-party API and assumes the response is always safe.

If the third-party service is compromised or returns unexpected data, the application could process the information unsafely.

**What should be verified:** 
Data received from external APIs should be treated as untrusted input and validated before being used.

**Impact:** 
Compromised third-party services can become a security risk to the application consuming them.

---

## Key Security Lessons

The most important lesson I learned is that API security requires more than authentication.

When reviewing an API, I should ask:

1. Who is making the request?
2. Is the user authenticated?
3. Is the user authorized to access this particular object?
4. Is the user authorized to perform this function?
5. Can the user modify sensitive properties?
6. Is the request properly validated?
7. Are resources and request frequency controlled?
8. Can business processes be abused?
9. Are external API responses treated as untrusted?
10. Are old or unnecessary API endpoints still exposed?

The main security question I would ask is:

**What is the API trusting, what should it verify, and what could happen if that verification fails?**


