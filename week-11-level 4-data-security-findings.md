# Data Security Threat Model

## PayGuard AI — STRIDE Threat Model

| STRIDE Category | My Finding |
|---|---|
| **Spoofing** | I found that the system trusts the tenant ID supplied with a request instead of checking whether it matches the authenticated user. This could allow someone to pretend to belong to another tenant and access their records. |
| **Tampering** | I discovered that the training pipeline does not properly verify training data before using it. Malicious or modified data could enter the pipeline and affect the model's behaviour. |
| **Repudiation** | I noticed that the system lacks sufficient approval and tracking of training-data changes. This could make it difficult to identify who introduced questionable data or approved its use. |
| **Information Disclosure** | I found that the shared vector database does not properly separate customer data. One tenant could retrieve another tenant's documents, and exposed embeddings could reveal information about the original records. |
| **Denial of Service** | I found that the retrieval system does not have effective limits on the number of queries it can process. Excessive requests could consume resources, increase costs, and slow down the service. |
| **Elevation of Privilege** | I discovered that changing the tenant ID in a request could allow a user to access records outside their authorized tenant. This means the system may grant more access than the user should have. |
| **OWASP Classification** | The findings relate to improper access control, data and model poisoning, insufficient data-integrity checks, and missing resource limits. The precise classifications should be confirmed against the project's rules and test evidence. |
| **Business Impact** | These weaknesses could expose confidential customer and financial information, damage customer trust, increase operating costs, and affect the reliability of the AI system. Poisoned training data could also cause unexpected model behaviour. |
| **Root Cause** | The system trusts incoming tenant IDs and training data without independently verifying them. Tenant separation is not properly enforced at the database or vector-store layer, and the retrieval pipeline lacks effective query limits. |
| **Remediation** | Enforce tenant isolation at the database or vector-store layer, derive tenant identity from the authenticated session, validate and approve training data before fine-tuning, restrict access to raw embeddings, enable query rate limiting, and rerun security tests to verify the fixes. |


