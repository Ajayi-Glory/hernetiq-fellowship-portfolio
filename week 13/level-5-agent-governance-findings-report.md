# LEVEL 5 – AGENT GOVERNANCE FINDINGS REPORT

## 1. Executive Summary

As part of Level 5 of the HerNetIQ AI Security Fellowship, I conducted a security assessment of LegalBot Municipal, a simulated AI-powered legal assistant designed to support municipal code-violation case reviews.

The assessment focused on the security risks associated with granting an AI agent excessive authority, particularly when it can perform consequential actions without sufficient human supervision.

The investigation highlighted three major areas of concern:

- Excessive agency: the AI assistant had the ability to finalize case dismissals without mandatory human approval.
- Indirect prompt injection: malicious instructions could be embedded in citizen-submitted comments and potentially influence the agent's behaviour.
- Insufficient input protection: citizen-provided content needed stronger sanitization and clear separation from trusted system instructions.

The exercise demonstrated why agentic AI systems require clearly defined permissions, strong governance controls and human oversight before consequential actions are performed.

## 2. Assessment Objectives and Scope

The primary objective was to identify weaknesses in LegalBot's agent configuration and decision-making workflow and recommend appropriate security controls.

The assessment covered:

1. Reviewing the agent's permissions and authority.
2. Identifying risks associated with autonomous case dismissal.
3. Examining indirect prompt injection through untrusted citizen comments.
4. Evaluating the need for human approval before consequential actions.
5. Applying the principle of least agency.
6. Mapping identified risks to the NIST AI Risk Management Framework and relevant agentic AI security concepts.

## 3. Key Security Findings

### Finding 1: Excessive Agency and Unrestricted Dismissal Authority

**Severity: Critical**

**Observation:**

The LegalBot configuration indicated that the AI assistant could finalize code-violation case dismissals without requiring human approval.

The configuration values identified in the assessment included:

- `CAN_FINALIZE_DISMISSAL = True`
- `REQUIRE_HUMAN_APPROVAL = False`

This created a risk that the AI agent could make consequential case-disposition decisions without an authorized human reviewing the outcome.

**Security Impact:**

An attacker or malicious input could potentially influence the agent into taking unauthorized actions. Incorrect or manipulated decisions could affect citizens and undermine the integrity of municipal processes.

**Recommended Remediation:**

- Remove unilateral dismissal authority from the AI agent.
- Require mandatory human approval before finalizing consequential case decisions.
- Apply least-agency principles to restrict the agent's permissions.
- Maintain an audit trail of requested actions, approvals and final decisions.

**Relevant Framework Mapping:**

- NIST AI RMF: GOVERN, MANAGE.
- OWASP Agentic AI: Excessive Agency and Inadequate Control of Agent Permissions, where applicable.

### Finding 2: Indirect Prompt Injection Through Citizen-Submitted Content

**Severity: High**

**Observation:**

The LegalBot scenario included a citizen comment containing instructions designed to manipulate the AI assistant.

The embedded content attempted to impersonate an authorized system instruction, claimed that compliance approval had already been granted and directed the agent to dismiss a case without further review.

This demonstrated the risk of treating untrusted citizen-submitted content as instructions rather than data.

**Security Impact:**

If the agent follows malicious instructions embedded in citizen comments, an attacker could manipulate its behaviour, bypass established workflows or influence consequential case decisions.

**Recommended Remediation:**

- Treat all citizen-submitted content as untrusted input.
- Clearly separate user-provided data from trusted system instructions.
- Apply input validation and sanitization.
- Ensure that instructions embedded in citizen comments cannot override system policies or authorization requirements.
- Require human review for sensitive actions.

**Relevant Framework Mapping:**

- NIST AI RMF: MAP, MEASURE and MANAGE.
- OWASP LLM Top 10: LLM01 – Prompt Injection.
- OWASP Agentic AI: Agent Goal Hijack, where applicable.

### Finding 3: Insufficient Human Oversight and Governance Controls

**Severity: High**

**Observation:**

The assessment identified a governance weakness in the process for authorizing consequential actions. The agent's workflow did not adequately enforce mandatory human review before case dismissals.

This created a gap between the authority given to the AI assistant and the level of accountability required for municipal decisions.

**Security Impact:**

Without effective human oversight, an AI assistant may execute an incorrect or manipulated decision without an authorized person having the opportunity to intervene.

This creates operational, legal, reputational and accountability risks.

**Recommended Remediation:**

- Introduce a mandatory human-in-the-loop approval process.
- Ensure that only authorized personnel can approve final case dispositions.
- Log every proposed action and its approval status.
- Prevent the agent from bypassing the approval workflow.
- Conduct periodic reviews of agent permissions and governance controls.

**Relevant Framework Mapping:**

- NIST AI RMF: GOVERN and MANAGE.
- OWASP Agentic AI: Excessive Agency, where the system's permissions permit uncontrolled consequential actions.

## 4. NIST AI Risk Management Framework Mapping

The assessment was aligned with the four core functions of the NIST AI Risk Management Framework.

| NIST Function | Application to LegalBot |
|---|---|
| GOVERN | Establish accountability, approval requirements and boundaries for AI agent permissions. |
| MAP | Identify the system's purpose, stakeholders, threat scenarios and potential consequences of unauthorized case dismissals. |
| MEASURE | Assess risks associated with excessive agency, prompt injection and insufficient human oversight. |
| MANAGE | Recommend permission restrictions, input protection, mandatory human approval and audit logging. |

The framework provided a structured approach for connecting technical security weaknesses with governance and operational risks.

## 5. Security Remediation and Technical Work

The remediation approach focused on reducing the agent's authority and strengthening the decision-making workflow.

The key security improvements considered included:

1. Restricting the AI agent's ability to independently finalize case dismissals.
2. Introducing mandatory human approval for consequential actions.
3. Strengthening the handling of untrusted citizen-submitted content.
4. Applying least-agency principles to agent permissions.
5. Improving accountability through action logging and review.

I also worked with the LegalBot Python implementation and the Semgrep static-analysis configuration.

**Technical validation performed:**

- Python compilation was checked using `python -m py_compile levels/level5_legalbot.py`.
- The Semgrep scan completed against the LegalBot file using the available configuration.
- The scan reported three rules executed and zero findings.
- The Streamlit application was successfully started on port 8501 and opened through the forwarded Codespaces address.

**Validation limitation:** The available Semgrep rules were focused on existing CartBot patterns rather than dedicated LegalBot security checks. Therefore, zero findings does not establish that LegalBot is free from vulnerabilities. Further targeted security rules and functional tests are required.

## 6. Residual Risks and Further Recommendations

Although the exercise focused on strengthening the security design, additional validation is necessary before considering the controls fully effective.

Recommended next steps include:

- Test whether malicious instructions in citizen comments are consistently rejected.
- Verify that case dismissals cannot be finalized without authorized human approval.
- Confirm that unauthorized users cannot access privileged agent actions.
- Test that all consequential actions are logged.
- Develop LegalBot-specific Semgrep rules.
- Review the agent's permissions periodically to prevent privilege expansion.

## 7. Conclusion

Level 5 reinforced an important lesson about agentic AI security: an AI system should not be given more authority than it needs to perform its assigned task.

The LegalBot assessment demonstrated how excessive permissions, indirect prompt injection and inadequate human oversight can combine to create serious security and governance risks.

Through this exercise, I strengthened my understanding of agentic AI security, threat modelling, prompt injection defence, least-agency principles, human-in-the-loop controls and NIST AI RMF mapping.

My biggest takeaway is that building secure AI systems requires more than protecting the underlying code. It also requires controlling what an AI agent is allowed to do, validating the information it receives and ensuring that consequential decisions remain accountable to humans.

**Core Lesson:** AI autonomy must always operate within clearly defined security boundaries, permissions and human accountability.

---

**Technical References**

- NIST AI Risk Management Framework: https://www.nist.gov/itl/ai-risk-management-framework
- OWASP Top 10 for LLM Applications: https://genai.owasp.org/llm-top-10/
- OWASP Agentic AI Security Initiative: https://genai.owasp.org/

**Portfolio Evidence**

- Level 5 Maestro Threat Model: https://github.com/Ajayi-Glory/hernetiq-fellowship-portfolio/blob/main/week%2013/level%205%20maestro%20threat%20model.md
- NIST AI RMF Mapping: https://github.com/Ajayi-Glory/hernetiq-fellowship-portfolio/blob/main/week%2013/nist-ai-rmf-mapping.md
- GitHub repository: https://github.com/Ajayi-Glory/hernetiq-fellowship-portfolio
