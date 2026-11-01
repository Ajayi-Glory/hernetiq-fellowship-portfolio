# LegalBot Municipal — Agent Governance

## Scenario

The Level 5 exercise examines LegalBot Municipal, an AI-powered assistant designed to support the city of Fairhaven. A malicious instruction hidden inside a citizen's comment attempts to manipulate the agent and trigger an unauthorised case dismissal.

## MAESTRO Threat Model

| MAESTRO Layer | My Finding |
|---|---|
| **L1 — Foundation Models** | The agent may interpret instructions hidden in a citizen's comment as commands instead of treating them as untrusted information. This creates a goal-hijacking risk because the agent could be redirected away from its intended purpose. |
| **L2 — Data Operations** | This layer is not directly implicated by the available evidence. The incident does not show a separate problem with the data collection, storage or processing pipeline. |
| **L3 — Agent Frameworks** | The agent could turn a manipulated instruction into an actual tool action because its configuration allows case dismissal without mandatory human approval. The workflow is missing an effective authorisation check before a sensitive action can be completed. |
| **L4 — Deployment and Infrastructure** | The supplied configuration shows that the agent has standing permission to dismiss cases. This is an application-level permission weakness. However, the available evidence does not establish that the underlying infrastructure was compromised. |
| **L5 — Evaluation and Observability** | The setting `LOG_AGENT_ACTIONS = False` shows that the intended agent-action logging is disabled in the vulnerable configuration. Without a reliable audit trail, it becomes more difficult to determine what happened, investigate suspicious activity and identify who or what initiated an action. |
| **L6 — Security and Compliance** | The system does not enforce mandatory human approval before a sensitive case decision. The organisation should establish clear rules defining which actions the agent can perform independently and which actions require human authorisation. |
| **L7 — Agent Ecosystem** | This layer is not applicable based on the available scenario. The incident involves a single AI agent and does not describe communication or interactions between multiple agents. |
| **OWASP Classification** | **ASI01 — Agent Goal Hijack:** A malicious instruction in a citizen comment attempts to redirect the agent. **ASI02 — Tool Misuse and Exploitation:** The agent's case-finalisation capability could be used to perform an unsafe or unauthorised action. **ASI03 — Identity and Privilege Abuse:** The agent has more authority than necessary for its normal support responsibilities. |
| **Business Impact** | An unauthorised dismissal could result in an incorrect municipal decision, harm public trust and reduce accountability. The absence of action logs could also make investigation and recovery more difficult. These are potential consequences of the simulated incident, not evidence that an actual citizen's case was dismissed. |
| **Root Cause** | The main weakness is the combination of excessive standing permissions and the absence of mandatory human approval. Although input sanitisation can help prevent goal hijacking, it cannot replace proper access control. The agent should not have independent authority to dismiss cases, even if its instructions are manipulated. |
| **Remediation** | Set `CAN_FINALIZE_DISMISSAL = False`, `REQUIRE_HUMAN_APPROVAL = True`, `SANITIZE_CITIZEN_INPUT = True` and `LOG_AGENT_ACTIONS = True`. Require human review for case decisions and enforce authorisation checks before sensitive actions are executed. Run the prescribed before-and-after tests and Semgrep scan, then save screenshots of the actual results as evidence. |

## Mapping to the OWASP Agentic Top 10

The LegalBot findings relate to the following OWASP Agentic Top 10 risk categories:

1. **ASI01 — Agent Goal Hijack:** A malicious instruction embedded in a citizen's comment attempts to change the agent's intended task. The agent may incorrectly treat untrusted content as an authorised instruction.

2. **ASI02 — Tool Misuse and Exploitation:** Once the agent's goal is manipulated, its case-finalisation capability could be used to perform an unsafe or unauthorised dismissal. The missing human-approval control increases this risk.

3. **ASI03 — Identity and Privilege Abuse:** LegalBot has standing dismissal permissions that exceed what it needs for its normal support role. Applying least privilege would remove this unnecessary authority instead of relying only on warnings or additional checks.

## Overall Security Recommendation

LegalBot should operate with limited permissions and should never independently finalise sensitive municipal case decisions. Citizen comments must be treated as untrusted input, sensitive actions must require human approval, and agent activities must be logged for accountability.

The organisation should verify the changes through security testing and retain the actual test and scan results as evidence.
