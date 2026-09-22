PayGuard AI — RAG Security Assessment

1. Poisoning Vectors

Retrieval-Time Poisoning

Retrieval-time poisoning happens when an attacker gets malicious or misleading content into the knowledge base or vector database.

For PayGuard, an attacker could submit a fake invoice or manipulated financial document that gets accepted into the RAG pipeline. Once the document is embedded and stored, it can be retrieved when a customer asks a related question.

### What the attacker does

The attacker introduces a malicious or inaccurate document into the PayGuard knowledge base. The document is then processed and made available to the retrieval system.

### What the user experiences

A customer may receive incorrect financial or payment-dispute advice because the AI retrieved the poisoned document as supporting context.

The response could still look reasonable because the AI is generating an answer from information that appears relevant.

### Blue Team indicator of compromise

The Blue Team should look for:

- Unexpected documents entering the knowledge base.
- Documents from unapproved sources.
- Changes to existing financial documents.
- Suspicious content or prompt-injection patterns inside documents.
- Retrieval results that conflict with trusted financial records.

### Training-Time Poisoning

Training-time poisoning affects the fine-tuning pipeline rather than the live knowledge base.

### What the attacker does

An attacker gets poisoned or deliberately manipulated data into the fine-tuning dataset. The model then learns behaviour from that data before deployment.

### What the user experiences

The model may repeatedly provide incorrect financial reasoning or payment advice, even when the correct information exists in the knowledge base.

This is different from retrieval-time poisoning because removing one document from the vector database may not correct behaviour that has already been learned by the model.

### Blue Team indicator of compromise

The Blue Team should monitor:

- Unexpected changes to the fine-tuning dataset.
- Unknown or unapproved data sources.
- Dataset provenance.
- Changes in model behaviour after training.
- Fine-tuning pipeline activity and validation results.

### PayGuard Comparison

| | Retrieval-Time Poisoning | Training-Time Poisoning |
|---|---|---|
| Affected area | Knowledge base / vector database | Fine-tuning dataset / model |
| Timing | After deployment | Before deployment |
| What is affected | Retrieved information | Learned model behaviour |
| Response | Remove and re-ingest clean data | Investigate, retrain or replace the model |

---

## 2. Embedding Model Audit

The embedding model is an important part of PayGuard because it converts financial documents and user questions into vectors used for retrieval.

I would assess the model using five behavioural trust checks.

### 1. Publisher Verification

The publisher should be identifiable and accountable.

A model from an unknown or anonymous source creates additional supply-chain risk because there may be no clear organisation responsible for the model.

### 2. Training Data Provenance

PayGuard should be able to understand what datasets were used to train the embedding model and whether those sources are appropriate for financial document retrieval.

### 3. Evaluation Results

The model should have evaluation or benchmark results demonstrating that it performs reliably for retrieval tasks.

Without evaluation evidence, it is difficult to determine whether the model is suitable for PayGuard.

### 4. Intended Use Documentation

The model documentation should clearly explain what the model was designed for and its limitations.

For PayGuard, the intended use should be compatible with financial-document retrieval.

### 5. Active Maintenance

The model should be maintained and updated when issues are discovered.

An abandoned embedding model can become a security and operational liability.

### Audit Decision

I would not approve an embedding model for PayGuard simply because the model file is safe to load.

The earlier Picklescan exercise focused on whether a model file was safe to load. This audit is different. It focuses on whether the model's learned behaviour, provenance, documentation and maintenance can be trusted.

For a production FinTech system, PayGuard should require evidence for all five trust checks before approving an embedding model.

---

## 3. Filter Enforcement Gap

The initial PayGuard application uses an application-level tenant check. The logic appears to be:

1. Get the tenant ID from the user session.
2. Check that a tenant ID exists.
3. Query the vector database.
4. Filter the returned results using the tenant ID.

At first this appears to provide tenant isolation, but there is an important weakness.

### Why the Application Filter Is Not Enough

The vector database retrieves the shared data before the application filters the results.

This means unauthorised tenant data has already been retrieved.

There is also another problem: the filter exists in application code. If an attacker can bypass the application and access the vector database directly, the application-level protection no longer exists.

### Live Demo Evidence

During the PayGuard interactive demo, the system showed a cross-tenant retrieval warning:

> "Cross-tenant retrieval — you are viewing another client's documents. No metadata filter was applied."

The demo showed that a user associated with one client could receive another client's financial document.

This demonstrates that tenant isolation was not being enforced at the retrieval layer.

### Why This Matters

PayGuard is a FinTech application containing sensitive financial information.

Allowing one tenant's documents to appear in another tenant's retrieval results creates a confidentiality risk and could expose customer financial information.

### Required Fix

Tenant isolation should be enforced inside the vector database query itself.

The database should only return documents whose `tenant_id` matches the authenticated tenant.

The security boundary should therefore look like:

User → Application → Vector Database → Tenant filter → Authorised documents

This means that even if the application layer is bypassed, the database still enforces the tenant boundary.

---

## 4. Recommendations

### Recommendation 1 — Enforce tenant isolation at the database layer

PayGuard should enforce the tenant ID as part of every vector database query.

The database should reject or exclude records belonging to another tenant before those records leave the database.

This prevents the application layer from being the only security boundary.

### Recommendation 2 — Validate data before ingestion

Every document entering the PayGuard knowledge base should be checked before it is embedded.

PayGuard should verify the document source, inspect the content for suspicious patterns and attach the correct tenant metadata before the document enters the vector database.

Untrusted documents should not be allowed into the knowledge base without validation.

### Recommendation 3 — Strengthen the AI supply chain

PayGuard should maintain evidence for the embedding model's publisher, training-data provenance, evaluation results, intended use and maintenance status.

The same approach should be applied to the fine-tuning pipeline so that poisoned or untrusted training data cannot silently change the model's behaviour.

---

## Conclusion

The PayGuard assessment showed that securing a RAG system requires more than securing the AI model.

The vector database, retrieval process, document-ingestion pipeline, embedding model and fine-tuning pipeline are all part of the security boundary.

The cross-tenant retrieval demonstration showed why application-level controls alone are not enough. If the database does not enforce tenant isolation, an attacker who bypasses the application can potentially access data belonging to other tenants.

PayGuard therefore needs security controls at the layer where the data actually lives, together with validation of the data and models entering the AI pipeline.
