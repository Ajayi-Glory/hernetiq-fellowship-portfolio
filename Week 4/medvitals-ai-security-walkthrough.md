
## 1. Introduction

As part of my HerNetIQ AI Security Fellowship, I completed a practical security investigation involving MedVitals AI, a healthcare AI application.

The exercise focused on identifying security weaknesses in application configuration, reviewing AWS deployment permissions, investigating suspicious cloud activity, and understanding how security vulnerabilities could be exploited.

My main objectives were to:

- Identify exposed credentials in application configuration.
- Investigate excessive AWS IAM permissions.
- Analyse 17 AWS CloudTrail log records.
- Identify indicators of compromise (IOCs).
- Reconstruct a potential attack sequence.
- Understand appropriate security remediation measures.
- Document my findings and security improvements.

This exercise helped me understand how application security and cloud infrastructure security work together to protect AI systems.

---

## 2. Lab Setup and Environment

I started by accessing the fellowship lab environment and locating the files required for the investigation.

The main files examined during the exercise were:

- `config.py`
- `deploy-role-policy.json`
- AWS CloudTrail JSON security logs.

I used Python and JSON inspection techniques to understand the configuration settings, examine cloud permissions, and investigate suspicious activity.

The investigation involved three main areas:

1. Application configuration security.
2. AWS Identity and Access Management (IAM).
3. CloudTrail security event analysis.

The goal was to identify weaknesses that could allow an attacker to gain unauthorised access or misuse cloud resources.

### Security tools and concepts

- Python
- JSON
- AWS IAM
- AWS CloudTrail
- GitHub
- Indicators of Compromise (IOCs)
- Principle of Least Privilege
- Incident investigation

---

## 3. Investigation of config.py

The first file I investigated was `config.py`.

Configuration files control important application settings and may contain sensitive information such as API credentials, database passwords, and session secrets.

During my review, I identified hardcoded sensitive credentials as a major security weakness.

### Security vulnerabilities identified

**1. Hardcoded API credentials**

Sensitive API keys stored directly in source code can be exposed through repository access, accidental sharing, or source-code leaks.

**2. Insecure secret management**

Storing sensitive values directly in configuration files makes it harder to control access and rotate credentials securely.

### Why this matters

If an attacker obtains exposed credentials, they may be able to access connected services, databases, or cloud resources.

In a healthcare AI environment, unauthorised access could expose sensitive information and affect the confidentiality and integrity of the system.

### Recommended remediation

Sensitive credentials should be removed from application source code and stored using environment variables or a secure secrets management service.

A safer Python configuration pattern is:

```python
import os

AWS_SECRET_ACCESS_KEY = os.getenv("AWS_SECRET_ACCESS_KEY")
DB_PASSWORD = os.getenv("DB_PASSWORD")
SESSION_SECRET = os.getenv("SESSION_SECRET")
```

This allows the application to retrieve sensitive values from its runtime environment instead of storing them directly in source code.

The environment variables must also be protected through appropriate access controls.

### Key lesson

Secrets management is an essential part of application security. Credentials should never be exposed unnecessarily in source code.

---

## 4. Investigation of deploy-role-policy.json

The second file I examined was `deploy-role-policy.json`.

This file contained AWS IAM permissions associated with the deployment role.

IAM policies determine which actions an identity can perform and which resources it can access.

During the review, I identified excessive permissions, including the security risk associated with wildcard access.

### Understanding wildcard permissions

An IAM policy containing:

```json
{
  "Action": "*",
  "Resource": "*"
}
```

can grant extremely broad access, depending on the policy's other conditions and the permissions available to the identity.

This creates a serious security concern because a compromised identity may be able to perform actions beyond its legitimate responsibilities.

### Potential security impact

Excessive permissions may allow an attacker to:

- Access sensitive cloud resources.
- Read or modify stored information.
- Modify deployed application components.
- Perform unauthorised administrative operations.
- Escalate privileges.

### Recommended remediation

I identified the need to apply the Principle of Least Privilege.

The deployment role should:

1. Have only the AWS actions necessary for deployment.
2. Be restricted to specific resources.
3. Avoid unnecessary administrative permissions.
4. Separate deployment responsibilities from general administration.
5. Be reviewed regularly.

The objective is to ensure that a deployment identity cannot access or modify resources outside its authorised responsibilities.

---

## 5. Investigation of 17 AWS CloudTrail Logs

One of the most important parts of the exercise was investigating 17 AWS CloudTrail log records.

AWS CloudTrail records activity within an AWS account, including API calls, identity activity, and changes to cloud resources.

I used the IOC 5 Ws approach to structure my investigation.

### The IOC 5 Ws

**WHO:** Who performed the action? I examined the identity or principal associated with each event.

**WHAT:** What action was performed? I reviewed the AWS event name and API operation.

**WHEN:** When did the activity occur? I examined the event timestamps to understand the sequence of events.

**WHERE:** Where did the activity originate? I reviewed source IP addresses and associated network information.

**WHY:** Why was the activity suspicious? I considered whether the action was consistent with the identity's expected responsibilities and normal system behaviour.

### Important CloudTrail activities

The investigation included reviewing activities such as:

- Authentication events.
- S3 object access.
- S3 bucket discovery.
- Lambda function code updates.
- IAM role assumption.
- Privileged cloud operations.

These activities helped me understand how an attacker might discover resources, obtain additional permissions, and interact with cloud infrastructure.

### Investigating privilege escalation

A significant finding from the CloudTrail investigation was suspicious privilege escalation activity involving an attempt to assume a more privileged AWS role.

The investigation highlighted the importance of examining:

- The identity requesting the role.
- The target IAM role.
- The source IP address.
- The API event name.
- The timestamp.
- Whether the requested permissions were appropriate.

The activity was associated with a Tor exit node, making the source network an important indicator for further investigation.

However, the use of Tor alone does not prove malicious activity. It must be assessed alongside the identity, requested permissions, and surrounding events.

### Why CloudTrail matters

CloudTrail provides evidence that helps security analysts understand what happened in a cloud environment.

It allows investigators to connect different activities and identify potentially suspicious behaviour.

This exercise taught me that a single log entry may not reveal the full incident. Analysing the relationship between multiple events is essential.

---

## 6. Reconstructing the Potential Attack Chain

After examining the configuration weaknesses, IAM permissions, and CloudTrail activity, I considered how the vulnerabilities could be connected.

The potential attack sequence was:

1. Exposure or compromise of an application credential.
2. Unauthorised use of an identity or cloud access credential.
3. Reconnaissance to identify available cloud resources.
4. Attempted assumption of a more privileged IAM role.
5. Potential misuse of excessive permissions.
6. Possible access to or modification of cloud resources.

This demonstrates how an exposed secret and excessive permissions can increase the impact of a security incident.

The investigation also showed why cloud monitoring and access control must work together.

**Important:** This is a reconstructed potential attack chain based on the security weaknesses and suspicious activity examined in the lab. It should not be interpreted as proof that every stage was successfully executed.

---

## 7. Security Remediation

After identifying the vulnerabilities, I examined the security improvements required to reduce the risks.

### Remediation 1: Secure application credentials

Hardcoded secrets should be removed from application source code.

Sensitive values should be stored using environment variables or an appropriate secrets management service.

If credentials have been exposed, they should be rotated and access reviewed.

### Remediation 2: Apply least privilege

Excessive IAM permissions should be restricted.

Deployment roles should only receive the permissions and resource access necessary to perform their assigned functions.

### Remediation 3: Strengthen role assumption controls

IAM role assumption should be restricted to authorised principals.

Trust policies and permission policies should be reviewed to prevent unnecessary access to privileged roles.

### Remediation 4: Improve CloudTrail monitoring

CloudTrail events should be monitored for:

- Unusual login activity.
- Unexpected role assumptions.
- Privileged API calls.
- Unauthorised resource access.
- Unexpected changes to application infrastructure.

### Remediation 5: Improve secret management

Sensitive credentials should be protected throughout the application lifecycle.

Access should be limited, secrets should be rotated when necessary, and credentials should never be committed to public repositories.

---

## 8. Verification and Security Validation

Security remediation must be verified rather than assumed to be successful.

The following checks are important when validating the improvements:

| Security Area | Verification Method | Expected Result |
|---|---|---|
| Application secrets | Inspect `config.py` | Sensitive credentials are not hardcoded. |
| Environment configuration | Check runtime configuration | Required secrets are supplied securely. |
| IAM permissions | Review the updated deployment policy | Permissions are limited to necessary actions and resources. |
| Role assumption | Review IAM trust relationships and CloudTrail events | Only authorised principals can assume privileged roles. |
| CloudTrail monitoring | Review security events | Suspicious activities can be identified and investigated. |
| GitHub security | Review committed files | No active credentials or sensitive secrets are exposed. |

A successful code review does not automatically prove that the cloud environment is secure. The updated IAM policy and runtime configuration should also be tested.

---

## 9. GitHub Commit and Portfolio Documentation

Version control is important because it provides a record of security-related changes.

I used GitHub as part of my fellowship portfolio to document the investigation and preserve the project evidence.

The portfolio should contain:

- The reviewed `config.py` file.
- The reviewed `deploy-role-policy.json` file.
- CloudTrail investigation findings.
- The reconstructed attack chain.
- Security remediation documentation.
- Supporting screenshots.

### Suggested Git commit message

```text
Week 4: Review exposed secrets and AWS IAM permissions
```

The final GitHub commit should contain the actual updated files and a clear explanation of the changes.

---

## 10. Level 1 Completion Evidence

The Level 1 completion evidence should be included in the portfolio to demonstrate progression through the fellowship exercise.

The evidence should include the actual completion screenshot or mission record.

This provides a reference for the completed learning activity and its associated security investigation.

---

## 11. What I Learned

Before this lab, I understood hardcoded credentials, excessive IAM permissions, privilege escalation, and CloudTrail logs mainly as theoretical cybersecurity concepts.

Working through the exercise made these risks more practical. I realised that exposing one credential in a configuration file could create an opportunity for an attacker, and that excessive permissions could make the consequences of a compromised identity much more serious. Investigating CloudTrail logs also taught me the importance of connecting events, examining identities, reviewing source addresses, and understanding the sequence of actions rather than looking at each event independently. Most importantly, I learned that securing an AI application is not only about protecting the AI model. The application code, cloud infrastructure, credentials, permissions, and monitoring systems all contribute to the overall security of the system.

---

## 12. Conclusion

The Week 4 MedVitals AI security exercise strengthened my understanding of application security, AWS IAM, cloud monitoring, and incident investigation.

I learned how to identify hardcoded credentials, recognise excessive cloud permissions, investigate suspicious CloudTrail activity, and apply the principle of least privilege.

The exercise also reinforced the importance of documenting security findings and validating remediation measures with evidence.

This experience has helped me develop a more practical understanding of AI infrastructure security and the importance of protecting the systems that support AI applications.

---

## 13. Lab Evidence and Screenshots

The original fellowship assignment requires seven screenshots documenting the practical work.

The following evidence should accompany this walkthrough:

1. Lab setup and project environment.
2. `config.py` investigation.
3. `deploy-role-policy.json` investigation.
4. CloudTrail log investigation.
5. CloudTrail event analysis and attack-chain reconstruction.
6. Security remediation and GitHub commit.
7. Level 1 completion evidence.

All screenshots should be genuine images captured during the lab.

---  
**Project:** MedVitals AI  
**Lab:** Week 4 – AI Infrastructure Security Investigation
