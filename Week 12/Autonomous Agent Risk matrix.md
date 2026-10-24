# Domain 5 — Session 24: Autonomous Agent Risk Matrix

## 1. Assignment Scenario

### AI Customer Support Agent

The AI Customer Support Agent is an autonomous AI application that uses a Large Language Model (LLM), external APIs, customer records, and stored conversation history to perform customer service tasks.

The agent can read customer messages, retrieve customer records, create support tickets, send emails, and issue refunds within a configured limit.

Although these capabilities improve efficiency and customer experience, they introduce security risks because the agent can access sensitive information and perform actions with real-world consequences. Potential threats include prompt injection, unauthorized access to customer records, excessive permissions, fraudulent refunds, unsafe API calls, sensitive information leakage, and conversation-history poisoning.

The objective of this assessment is to identify at least seven realistic risks, explain their potential impacts, propose defensive controls, map each risk to a MAESTRO architectural layer, and identify the relevant NIST AI Risk Management Framework (AI RMF) function.

## 2. Autonomous Agent Risk Matrix

| Risk | Threat / Scenario | Impact | Defensive Control | MAESTRO Layer | NIST AI RMF Function |
|---|---|---|---|---|---|
| **1. Prompt Injection (ASI01)** | A malicious customer sends a message instructing the agent to ignore its system instructions, reveal confidential information, or perform unauthorized actions. | The agent may disclose sensitive information, bypass its intended workflow, or execute unauthorized actions, resulting in privacy breaches and financial losses. | Treat customer messages and retrieved documents as untrusted input. Separate trusted instructions from user content, test against prompt injection, restrict tool permissions, and validate sensitive actions independently of the LLM. | **Layer 3 — Agent Frameworks** | **Manage** — implement safeguards, restrict actions, and respond to identified risks. |
| **2. Unauthorized Customer-Record Access** | The agent retrieves another customer's records because identity verification or record-level authorization is missing or incorrectly implemented. | Exposure of personally identifiable information (PII), privacy violations, regulatory penalties, and loss of customer trust. | Enforce server-side authentication and record-level authorization on every request. Verify customer identity and ownership before returning records, and log access attempts. | **Layer 2 — Data Operations** | **Map** — identify sensitive data, access pathways, affected stakeholders, and potential privacy harms. |
| **3. Excessive Agent Permissions (ASI03)** | The agent possesses broad API credentials or administrative permissions that allow it to access or modify resources beyond its intended responsibilities. | A compromised or manipulated agent could expose large volumes of data, modify customer accounts, or cause widespread system damage. | Apply least privilege, role-based access control, short-lived scoped credentials, permission boundaries, and regular access reviews. Separate read-only permissions from transaction and administrative permissions. | **Layer 7 — Agent Ecosystem** | **Govern** — establish access policies, accountability, and authorization requirements. |
| **4. Unauthorized or Fraudulent Refunds** | The agent issues refunds without verifying eligibility, repeats a transaction, or attempts to exceed its configured refund limit. | Financial losses, fraudulent transactions, customer disputes, and accounting discrepancies. | Enforce refund limits in backend services rather than relying on the LLM. Validate transaction status, use idempotency keys to prevent duplicate refunds, and require human approval for high-value or suspicious transactions. | **Layer 3 — Agent Frameworks** | **Manage** — apply transaction safeguards, approval workflows, and risk-response procedures. |
| **5. Unsafe External API Calls (ASI02)** | The agent invokes an unintended tool or sends incorrect, manipulated, or malformed parameters to a customer-management, email, or payment API. | Unauthorized account changes, data disclosure, incorrect transactions, and disruption of customer-support operations. | Maintain an allowlist of approved tools, enforce strict input and output schemas, validate parameters, check authorization server-side, and restrict each tool to necessary operations. | **Layer 3 — Agent Frameworks** | **Measure** — test and evaluate tool behavior, validation failures, and control effectiveness. |
| **6. Sensitive Information Leakage** | The agent includes confidential customer details, payment information, authentication secrets, or internal records in a response or email sent to an unauthorized recipient. | Identity theft, financial harm, privacy violations, legal exposure, and reputational damage. | Apply data minimization, redact sensitive information, validate email recipients, filter model outputs, and prevent secrets from entering prompts and logs. Test the agent with sensitive-data leakage scenarios. | **Layer 5 — Evaluation and Observability** | **Measure** — evaluate leakage risks, monitor outputs, and test the effectiveness of privacy safeguards. |
| **7. Conversation-History Poisoning (ASI06)** | An attacker inserts misleading or malicious information into stored conversation history or agent memory so that future decisions are influenced by untrusted content. | Persistent manipulation, repeated incorrect responses, disclosure of information, and unsafe actions across multiple conversations. | Validate information before storing it in memory, distinguish verified records from user-provided claims, restrict memory modification, apply retention policies, and audit changes to stored context. | **Layer 2 — Data Operations** | **Manage** — implement memory-integrity safeguards, remediation procedures, and ongoing monitoring. |

## 3. MAESTRO Architectural Layer Mapping

The MAESTRO framework helps analyze security threats across the architecture of agentic AI systems. The seven layers are:

1. **Foundation Models** — the underlying models responsible for reasoning and generating responses.
2. **Data Operations** — data ingestion, storage, retrieval, memory, and processing.
3. **Agent Frameworks** — agent orchestration, planning, workflows, and tool execution.
4. **Deployment and Infrastructure** — hosting environments, compute resources, networks, and runtime services.
5. **Evaluation and Observability** — testing, monitoring, logging, and performance evaluation.
6. **Security and Compliance** — security safeguards, privacy controls, compliance requirements, and protection mechanisms.
7. **Agent Ecosystem** — external agents, third-party integrations, dependencies, and interconnected services.

### Why the selected MAESTRO layers are relevant

- **Agent Frameworks:** Prompt injection, refund abuse, and unsafe tool calls affect how the agent interprets instructions, selects tools, and executes actions.
- **Data Operations:** Unauthorized customer-record access and conversation-history poisoning involve access to sensitive data and the integrity of stored information.
- **Agent Ecosystem:** Excessive permissions can expose connected systems and external services to unauthorized operations.
- **Evaluation and Observability:** Sensitive information leakage must be detected and assessed through output testing, monitoring, and evaluation.

Each risk is assigned its primary architectural layer. In practice, some threats span multiple layers. For example, prompt injection may originate in the Foundation Models layer but exploit weaknesses in Agent Frameworks or Security and Compliance.

## 4. NIST AI RMF Governance Mapping

The NIST AI Risk Management Framework contains four core functions.

| NIST AI RMF Function | Purpose | Application to the AI Customer Support Agent |
|---|---|---|
| **Govern** | Establish policies, accountability, roles, responsibilities, and risk-management practices. | Define agent permissions, refund approval policies, privacy requirements, and ownership of security controls. |
| **Map** | Establish the context of the AI system and identify potential risks, stakeholders, and impacts. | Identify sensitive customer data, record-access pathways, affected customers, and potential privacy harms. |
| **Measure** | Analyze, assess, test, and monitor AI risks and control effectiveness. | Test for data leakage, evaluate API validation, monitor suspicious activity, and assess whether safeguards work. |
| **Manage** | Prioritize risks and implement responses, mitigations, and ongoing risk-management activities. | Restrict permissions, enforce refund limits, implement prompt-injection safeguards, and respond to detected incidents. |

The NIST function assigned to each risk in the matrix represents its primary governance emphasis. Organizations may apply multiple functions to the same risk throughout the AI lifecycle. Govern is cross-cutting and supports the other functions.

## 5. Quick Assessment

### Question 1: What is ASI01?

ASI01 is **Agent Goal Hijack**. It occurs when an attacker manipulates an agent's instructions or inputs to redirect it away from its intended objectives. For example, a malicious customer could use prompt injection to try to make the support agent reveal confidential records or bypass refund policies.

### Question 2: What is the difference between ASI02 and ASI03?

- **ASI02 — Tool Misuse and Exploitation:** The agent's legitimate tools or integrations are misused or exploited to perform unintended or unsafe operations.
- **ASI03 — Identity and Privilege Abuse:** An attacker exploits weaknesses in authentication, identity management, or authorization to impersonate an identity or gain excessive privileges.

The key difference is that ASI02 focuses on unsafe tool use, while ASI03 focuses on identity and permission abuse.

### Question 3: Why does an autonomous agent create an action surface?

An autonomous agent creates an action surface because it can interact with external systems and perform operations beyond generating text. Every API, tool, credential, permission, and connected service creates a potential pathway through which an attacker could influence the system or trigger unauthorized actions.

### Question 4: What does schema enforcement provide?

Schema enforcement ensures that inputs and outputs conform to a predefined structure, expected data types, required fields, and validation rules. It helps reject malformed requests and unexpected parameters before they reach downstream systems.

However, schema enforcement alone cannot determine whether an otherwise valid request is authorized. It must be combined with authentication, authorization, business-rule validation, and appropriate approval controls.

### Question 5: What is the role of Llama Guard?

Llama Guard is a safety classification model that evaluates text, such as user prompts and model responses, against defined safety categories. It can help identify potentially unsafe content and support decisions to allow or block it.

It is a defense-in-depth component and does not replace secure API design, access controls, or authorization checks.

### Question 6: How does NeMo Guardrails differ from a safety classifier?

A safety classifier evaluates content and classifies it according to specified safety categories. NeMo Guardrails is a broader framework for implementing programmable rules that govern conversational behavior and, when configured, interactions with tools.

A classifier focuses primarily on content classification, whereas a guardrails framework can enforce rules across conversational flows. Neither guarantees complete protection against all attacks.

### Question 7: Name the seven MAESTRO layers.

1. Foundation Models
2. Data Operations
3. Agent Frameworks
4. Deployment and Infrastructure
5. Evaluation and Observability
6. Security and Compliance
7. Agent Ecosystem

### Question 8: Name the four NIST AI RMF functions.

1. **Govern** — establish accountability, policies, and oversight.
2. **Map** — identify system context, stakeholders, and potential risks.
3. **Measure** — assess and monitor risks and control effectiveness.
4. **Manage** — prioritize risks and implement appropriate responses.

### Question 9: Why should the risk matrix contain both technical and governance mappings?

Technical mappings identify the architectural location of a risk and help determine which technical safeguards are needed to reduce it. Governance mappings establish how the organization should assess, prioritize, oversee, and manage that risk.

Both are necessary because technical safeguards alone do not establish organizational accountability, acceptable-risk policies, or oversight. Combining MAESTRO and NIST AI RMF provides a more complete approach to managing autonomous-agent security risks.

## 6. Evidence of Mastery

The assessment follows a structured risk-analysis process:

**AI application → Agent capabilities → Threat identification → Impact assessment → MAESTRO mapping → NIST AI RMF mapping → Defensive control**

### Step 1: Identify the AI application's capabilities

The agent reads customer messages, retrieves records, creates support tickets, sends emails, and issues refunds.

### Step 2: Identify what could go wrong

Potential threats include prompt injection, unauthorized access, excessive permissions, fraudulent refunds, unsafe tool calls, information leakage, and memory poisoning.

### Step 3: Explain the impact

Each threat is linked to realistic consequences, including privacy breaches, financial losses, unauthorized account changes, operational disruption, and reputational damage.

### Step 4: Locate the technical risk

MAESTRO identifies the primary architectural layer associated with each threat, helping security practitioners determine where controls should be implemented.

### Step 5: Determine how the organization should manage the risk

NIST AI RMF provides a governance perspective through Govern, Map, Measure, and Manage. These functions support accountability, contextual risk identification, testing, monitoring, and mitigation.

### Step 6: Propose a defensive control

Each risk is paired with a practical control, such as least-privilege permissions, server-side authorization, schema enforcement, refund limits, output filtering, and memory-integrity validation.

## 7. Conclusion

Autonomous AI agents introduce security challenges because they combine language-model reasoning with access to sensitive data, external tools, and systems that can perform real-world actions.

The seven risks identified in this assessment demonstrate the importance of protecting agent instructions, customer records, permissions, tool integrations, outputs, and stored memory.

The MAESTRO framework helps identify where technical risks exist within the agent architecture, while the NIST AI RMF provides a structured approach to organizational governance and risk management. Implementing defense-in-depth controls, continuously testing agent behavior, enforcing least privilege, and requiring human approval for sensitive operations can significantly reduce the likelihood and impact of agentic AI security incidents.

