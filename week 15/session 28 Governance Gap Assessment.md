## Executive Summary

This assessment examines important governance risks that can arise when an organisation uses machine learning and artificial intelligence systems in a regulated financial environment.

The assessment focuses on whether the organisation's documented AI governance requirements are supported by effective technical controls, clear accountability, risk monitoring and human oversight.

Three potential governance gaps have been identified for investigation: insufficient validation of external training data, weaknesses in cross-tenant access controls, and automated model deployment without adequate human review.

These weaknesses could expose the organisation to data integrity problems, unauthorised disclosure of customer information, biased model outputs and regulatory non-compliance.

The organisation should strengthen its data approval procedures, verify tenant isolation controls and introduce formal approval requirements before deploying AI models into production.

## Assessment Scope

The assessment is intended to cover:

- The organisation's AI governance policy and documented procedures.
- Production code responsible for data validation, access control and model deployment.
- Audit logs showing relevant system activities and security events.
- The relationship between documented governance requirements and actual technical implementation.

**Evidence limitation:** The actual policy document, production code and audit logs have not yet been supplied for review. Specific file names, line references and audit events must therefore be added after examining the original evidence.

## Framework Used

The NIST AI Risk Management Framework is used as the primary assessment structure.

- **GOVERN:** Establishes accountability, policies and oversight.
- **MAP:** Identifies and contextualises AI-related risks.
- **MEASURE:** Evaluates and monitors AI risks.
- **MANAGE:** Implements controls and responds to identified risks.

The assessment also considers relevant OWASP Top 10 for Large Language Model Applications categories, the EU AI Act and ISO/IEC 42001.

---

## Governance Findings

### Finding A — External Training Data Is Not Properly Verified

**Severity: High**

**Policy claim:** External data sources should receive documented approval before being included in the AI training pipeline.

**Observed weakness to investigate:** The training example describes a validation process that accepts external sources without completing the required integrity checks, schema validation and bias review.

If this behaviour is confirmed in the production code, unverified or manipulated data could enter the training pipeline and affect model reliability.

**Business impact:** Models trained using unverified data may produce inaccurate or biased results. This could affect financial decisions, customer outcomes and the organisation's regulatory obligations.

**NIST AI Risk Management Framework mapping:**
- MAP — Training data risks must be identified and assessed.
- MEASURE — Data integrity and quality must be evaluated.
- MANAGE — Controls must prevent unapproved data from entering the pipeline.

**OWASP mapping:** LLM03 — Training Data Poisoning.

**Regulatory exposure:**
- EU AI Act Article 10 — Data and data governance requirements for high-risk AI systems.
- ISO/IEC 42001 — AI data management and risk assessment controls.

**Recommended remediation:**

The CTO should establish a formal external data approval process within 30 days. The process should include source verification, integrity checks, schema validation, bias assessment and documented human approval before external data is used for training.

Evidence of approval should be retained for audit purposes.

**Evidence citation required:** AI governance policy clause, data-validation code file and relevant audit-log entry.

### Finding B — Cross-Tenant Data Isolation May Be Inadequate

**Severity: Critical**

**Policy claim:** Customer information must remain isolated so that one tenant cannot access another tenant's data.

**Observed weakness to investigate:** The training materials identify cross-tenant API access control as an existing security topic. The production implementation must be examined to determine whether customer identity and tenant ownership are verified on every relevant request.

If the system relies only on a customer ID supplied by the requester without checking authenticated ownership, one customer could potentially access another customer's information.

**Business impact:** Unauthorised access to financial or personal customer information could result in a serious data breach, loss of customer trust, legal exposure and regulatory penalties.

**NIST AI Risk Management Framework mapping:**
- GOVERN — Clear access-control responsibilities must be established.
- MAP — Cross-tenant exposure must be identified.
- MEASURE — Tenant isolation must be tested.
- MANAGE — Unauthorised access must be prevented and addressed.

**OWASP mapping:** LLM06 — Sensitive Information Disclosure, where sensitive information is exposed through the AI application.

**Regulatory exposure:**
- EU General Data Protection Regulation (GDPR), Article 32 — Security of processing, where applicable.
- ISO/IEC 42001 — AI system security and risk management controls.

**Recommended remediation:**

The engineering team should enforce tenant isolation at the API and database layers. Every request must derive the user's identity from a verified authentication mechanism and confirm that the requested record belongs to the authenticated tenant.

The team should complete cross-tenant access tests within 14 days and retain the test results as evidence.

**Evidence citation required:** Relevant authentication and database-query code, access-control policy and audit-log records.

### Finding C — AI Model Deployment May Lack Human Approval

**Severity: High**

**Policy claim:** AI systems should undergo appropriate review and risk assessment before being deployed into production.

**Observed weakness to investigate:** The training materials identify automated deployment without review as a governance risk. The deployment workflow must be examined to establish whether model releases can proceed without an authorised human approval step.

If this weakness is confirmed, an unsafe, biased or inadequately tested model could enter production without sufficient oversight.

**Business impact:** Unreviewed models may produce unreliable financial outputs, create operational disruption and expose the organisation to regulatory and reputational damage.

**NIST AI Risk Management Framework mapping:**
- GOVERN — Deployment responsibilities and approval authority must be defined.
- MEASURE — Model performance and risks must be evaluated before release.
- MANAGE — Deployment must be controlled through approval and rollback procedures.

**OWASP mapping:** LLM09 — Overreliance, where users or systems place excessive trust in model outputs without appropriate verification.

**Regulatory exposure:**
- EU AI Act Article 14 — Human oversight for high-risk AI systems.
- EU AI Act Article 9 — Risk management system requirements.
- ISO/IEC 42001 — AI system lifecycle management and operational controls.

**Recommended remediation:**

The CTO and engineering team should introduce a documented model-release approval process within 30 days. Production deployment should require completed risk assessments, testing evidence, an authorised approval and a rollback plan.

Deployment records should identify the approving person, model version, date and test results.

**Evidence citation required:** Deployment workflow, model-release policy and relevant audit-log entries.

---

## Regulatory Exposure Summary

| Finding | Framework or regulation | Relevant requirement |
|---|---|---|
| External training data validation | EU AI Act Article 10 | Data governance and data quality for high-risk AI |
| External training data validation | ISO/IEC 42001 | AI data management and risk controls |
| Cross-tenant data isolation | GDPR Article 32 | Appropriate security of personal data processing |
| Cross-tenant data isolation | ISO/IEC 42001 | AI system security and risk management |
| Uncontrolled model deployment | EU AI Act Articles 9 and 14 | Risk management and human oversight |
| Uncontrolled model deployment | ISO/IEC 42001 | AI lifecycle and operational governance |

*Applicability depends on the organisation's activities, jurisdiction, system classification and regulatory obligations.*

## Overall Risk Assessment

The potential weaknesses described in this assessment indicate that an organisation may have documented AI governance requirements without sufficiently effective technical and operational controls.

The most serious potential exposure is cross-tenant data access because it could result in unauthorised disclosure of customer information.

External data validation and uncontrolled model deployment also require attention because they can affect model integrity, reliability and accountability.

## Overall Verdict

The AI governance programme cannot yet be confirmed as fit for purpose because the actual implementation evidence has not been reviewed. The organisation should demonstrate that its policies are supported by enforceable technical controls, documented approvals, continuous monitoring and clear accountability before making that claim to investors or regulators.

## Priority Recommended Actions

- **Within 14 days — Engineering:** Verify tenant isolation and complete cross-tenant access testing.
- **Within 30 days — CTO and Data Governance Team:** Establish external data approval and validation procedures.
- **Within 30 days — CTO and Engineering Team:** Introduce mandatory human approval before production model deployment.
- **Ongoing — AI Governance Team:** Maintain audit records, monitor model performance and review governance controls regularly.

## Evidence Register — To Complete Before Submission

| Required evidence | Status |
|---|---|
| AI governance policy and section references | Awaiting review |
| External data validation source code and line references | Awaiting review |
| Tenant authentication and isolation code | Awaiting review |
| Model deployment workflow | Awaiting review |
| Relevant audit logs | Awaiting review |
| Remediation verification results | Awaiting testing |

**Final assessment note:** Findings in this preliminary draft are assessment hypotheses based on the training examples. They must not be represented as independently verified company audit findings until the original evidence has been examined.
