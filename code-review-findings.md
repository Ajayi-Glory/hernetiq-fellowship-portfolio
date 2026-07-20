Week 2 – Code Review Findings: MedVitals AI
1. What This Script Does
The MedVitals AI script is designed to retrieve a patient's medical information and generate a summary using an AI model. It connects to an AI service, processes patient records, and returns the generated response for healthcare staff to review.

Because this application handles sensitive patient information and connects to cloud resources, protecting credentials and limiting permissions are essential to maintaining the security of the system.

2. Security Findings
Finding 1 – Hardcoded Credentials
**Line(s):Section 1 – API Credentials

Code Reference:

AI_MODEL_KEY = "sk-medvitals-prod-..."
DATABASE_URL = "postgresql://admin:Hospital2024!@..."
INTERNAL_TOKEN = "Bearer eyJhbGciOi..."
**What it is: Sensitive credentials, including the AI API key, database connection string, and internal authentication token, are written directly into the source code instead of being stored securely.

**Why it matters: Anyone with access to the source code can immediately obtain these secrets and use them to access the AI service, internal systems, or production database without authorization.

**Business Impact: A compromised API key or database credential could expose confidential patient records, disrupt hospital operations, generate unexpected cloud costs, and damage the organization's reputation while creating legal and regulatory consequences.

**Fix: Store all secrets in environment variables or a secure secret management solution and retrieve them using:

os.environ.get("AI_MODEL_KEY")
Finding 2 – Overly Permissive IAM Policy
**Line(s): Section 2 – Cloud Permissions

**Code Reference:

IAM_POLICY = {
    "Effect": "Allow",
    "Action": "*",
    "Resource": "*"
}
**What it is: The IAM policy grants unrestricted permissions by allowing every action on every cloud resource.

**Why it matters: If an attacker gains access to the application or its credentials, they could perform any operation within the cloud environment, including deleting resources, accessing confidential data, or creating new infrastructure.

**Business Impact: This level of access could lead to data breaches, service outages, financial losses from cloud abuse, and complete compromise of the organization's cloud environment.

**Fix: Apply the Principle of Least Privilege by granting only the minimum permissions required for the application to perform its intended tasks.

Finding 3 – Missing Secret Management
**Line(s):Section 4 – Startup Sequence

Code Reference:

print(f"Connected to: {DATABASE_URL}")
print(f"AI Key: {AI_MODEL_KEY}")
**What it is: Sensitive credentials are printed directly to terminal logs during application startup.

**Why it matters: Application logs are often stored, monitored, or shared with administrators. Printing secrets into logs makes those credentials accessible to anyone with log access.

**Business Impact: Leaked credentials in log files increase the likelihood of unauthorized access, compromise sensitive patient information, and may result in costly incident response and regulatory penalties.

**Fix: Never log passwords, API keys, database connection strings, or authentication tokens. Log only non-sensitive operational information.

Overall Risk Rating
The script contains multiple high-risk security weaknesses, including hardcoded credentials, unrestricted cloud permissions, and exposed secrets in application logs. If deployed in a production healthcare environment, these vulnerabilities could allow attackers to gain unauthorized access to sensitive patient records, compromise cloud infrastructure, interrupt critical services, and cause significant financial, legal, and reputational damage. These issues should be remediated before the application is deployed.

4. Reviewed By
Ajayi Glory Nwakaego
HerNetiQ AI Security Fellowship, Cohort 1, 2026
