### 1. My role and contribution

I contributed to the AI and Model Security workstream of the ShipSecure assessment of Zuri Bank's Ama assistant. My responsibilities included investigating customer-data isolation in Retrieval-Augmented Generation (RAG), reviewing potential indirect prompt-injection risks in the support workflow, and examining model aliases and fallback configuration.

### 2. What I investigated

I investigated whether Ama's RAG retrieval process consistently restricted customer account summaries to the correct customer. During controlled testing with synthetic customer records in local staging, I reproduced cross-customer retrieval, demonstrating that one customer's request could retrieve another customer's account summary.

I also reviewed the potential risk of malicious instructions embedded in customer-submitted or external support content influencing the AI system. This was identified as a potential prompt-injection risk; I did not establish that it successfully triggered an unauthorised financial action.

In addition, I examined concerns relating to model aliases and fallback configuration and identified the need for stronger controls over approved model sources and model versions.

### 3. Evidence I produced

I implemented a staging remediation for the RAG customer-data isolation finding. The changes introduced customer-specific filtering for account summaries, preserved access to shared FAQs, and updated the automated tests.

The work covered:
- `zuri/agent.py`
- `zuri/rag/ingest.py`
- `zuri/rag/retriever.py`
- `tests/test_smoke.py`

**Evidence references:**
- Git commit: `eaddf84` – Fix RAG customer data isolation.
- Feature branch: `FIX/RAG-CUSTOMER-DATA-ISOLATION`.
- Test evidence: `15 passed`.
- GitHub evidence: successful push of the feature branch.
- Supporting screenshots: commit, push, test results and Python syntax checks.

The remediation was implemented and tested in local staging. I did not claim that it had been merged or deployed to production.

### 4. What I would do differently

I would establish and verify the authenticated customer identity and production retrieval architecture earlier in the assessment. This would help distinguish staging-level controls from production security guarantees and make the evidence and remediation validation more complete.

**Overall contribution:** This work helped identify and address a customer-data isolation weakness, document AI-related security risks, and provide evidence-based recommendations for improving Ama's security.
