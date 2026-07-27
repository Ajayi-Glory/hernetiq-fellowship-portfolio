Hardened Cloud IAM Framework

1. Incident Summary

On 10 July 2026, someone gained unauthorized access to the MedVitals AWS account by using the IAM user **medvitals-backup-user**. From the CloudTrail logs, I noticed the attacker first confirmed the account, checked the EC2 instances, created a new IAM user, and then gave that user AdministratorAccess. This allowed the attacker to keep full access to the AWS environment.

2. Attack Timeline

Event 1

Timestamp: 2026-07-10T01:12:44Z

Event Name: GetCallerIdentity

Evidence

```json

{

  "eventTime": "2026-07-10T01:12:44Z",

  "eventName": "GetCallerIdentity",

  "userIdentity": {

    "userName": "medvitals-backup-user"

  },

  "sourceIPAddress": "91.108.56.177"

}

```

Description

The attacker used **GetCallerIdentity** to check that the stolen AWS credentials were valid before doing anything else.

---

Event 2

Timestamp: 2026-07-10T01:14:09Z

Event Name: DescribeInstances

Evidence

```json

{

  "eventTime": "2026-07-10T01:14:09Z",

  "eventName": "DescribeInstances"

}

```

Description

The attacker viewed the EC2 instances to understand what resources were available in the AWS environment.

---

Event 3

Timestamp: 2026-07-10T01:19:31Z

Event Name: CreateUser

Evidence

```json

{

  "eventTime": "2026-07-10T01:19:31Z",

  "eventName": "CreateUser",

  "requestParameters": {

    "userName": "medvitals-support-svc"

  }

}

```

Description

The attacker created a new IAM user called **medvitals-support-svc**. I believe this was done so they could still access the account even if the original user was discovered.

---

Event 4

Timestamp: 2026-07-10T01:26:58Z

Event Name: AttachUserPolicy

Evidence

```json

{

  "eventTime": "2026-07-10T01:26:58Z",

  "eventName": "AttachUserPolicy",

  "requestParameters": {

    "userName": "medvitals-support-svc",

    "policyArn": "arn:aws:iam::aws:policy/AdministratorAccess"

  }

}

```

Description

The attacker attached the **AdministratorAccess** policy to the new IAM user. This gave the account full administrative permissions over the AWS environment.

3. Root Cause Analysis

Looking at the logs, the compromised IAM user had permissions that made the attack possible. These permissions allowed the attacker to confirm the account, view resources, create another IAM user, and assign administrator privileges.

| Event | IAM Permission |


| GetCallerIdentity | `sts:GetCallerIdentity` |

| DescribeInstances | `ec2:DescribeInstances` |

| CreateUser | `iam:CreateUser` |

| AttachUserPolicy | `iam:AttachUserPolicy` |

If the account had only the permissions it actually needed, the attacker would not have been able to create a new user or grant administrator access.



5. Recommendations

To prevent this from happening again, I would disable the compromised **medvitals-backup-user** account immediately and delete the unauthorized **medvitals-support-svc** account. I would also rotate all access keys, enable Multi-Factor Authentication (MFA) for privileged users, apply the Principle of Least Privilege, and monitor CloudTrail for unusual IAM activities. Regular permission reviews should also be carried out to make sure users only have the access they need.
