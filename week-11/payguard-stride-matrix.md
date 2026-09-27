Full PayGuard STRIDE Matrix
## Overview

This assessment applies the STRIDE threat-modeling framework to the PayGuard RAG system.

The assessment covers the user interaction layer, data ingestion, the Airflow pipeline, the embedding model, the vector database, the AI/RAG assistant, the knowledge base, and the fine-tuning pipeline.

The assessment also carries forward the cross-tenant retrieval issue identified in Week 10 and extends the threat model to the training-time pipeline.

---

## STRIDE Matrix

| Component | STRIDE Category | Threat | Plain-English Explanation | Severity |
|---|---|---|---|---|
| User / Client | Spoofing | User identity spoofing | An attacker could attempt to impersonate another PayGuard user or tenant and access information belonging to another customer. | High |
| User / Client | Tampering | Query manipulation | A malicious user could modify questions or inputs to influence what the RAG system retrieves or how the AI responds. | Medium |
| User / Client | Repudiation | Untraceable requests | If user actions are not logged properly, a user could deny making a request that caused sensitive data to be retrieved. | Medium |
| User / Client | Information Disclosure | Cross-tenant data exposure | A manipulated tenant identifier or request could cause documents belonging to another customer to be returned. | High |
| User / Client | Denial of Service | Excessive requests | An attacker could send a large number of queries and consume retrieval or AI resources. | Medium |
| User / Client | Elevation of Privilege | Unauthorized tenant access | By manipulating identity or tenant information, an attacker could gain access to data outside their authorized tenant. | High |
| Data Source | Spoofing | Untrusted source impersonation | An attacker could make malicious information appear to come from a trusted PayGuard data source. | High |
| Data Source | Tampering | Poisoned input data | Malicious data could be inserted into the source feed and later become part of the knowledge base or training data. | High |
| Data Source | Repudiation | Missing source provenance | Without reliable records showing where data came from, it may be difficult to determine who supplied or modified the data. | Medium |
| Data Source | Information Disclosure | Sensitive source data exposure | Confidential customer or financial information could be exposed before it reaches the RAG or training pipeline. | High |
| Data Source | Denial of Service | Malicious data flooding | An attacker could submit excessive or very large amounts of data and consume ingestion resources. | Medium |
| Data Source | Elevation of Privilege | Unauthorized data submission | A compromised or external party could submit information to a trusted ingestion pipeline and influence downstream processing. | High |
| Airflow DAG | Spoofing | Pipeline identity spoofing | An attacker could impersonate a trusted pipeline component or job and introduce unauthorized data-processing activity. | High |
| Airflow DAG | Tampering | Pipeline modification | Changes to the DAG or its inputs could cause malicious data to be collected, processed, or sent to downstream components. | High |
| Airflow DAG | Repudiation | Missing pipeline audit trail | Without sufficient logging, it may be difficult to determine who changed or executed a pipeline task. | Medium |
| Airflow DAG | Information Disclosure | Pipeline data exposure | Logs, task outputs, credentials, or configuration files could expose sensitive information processed by the pipeline. | High |
| Airflow DAG | Denial of Service | Pipeline disruption | An attacker could disrupt scheduled tasks or consume pipeline resources, preventing normal data processing. | Medium |
| Airflow DAG | Elevation of Privilege | Excessive pipeline permissions | A pipeline with excessive permissions could allow an attacker who compromises it to access or modify other systems. | High |
| Embedding Model | Spoofing | Model source impersonation | An attacker could provide a malicious embedding model while making it appear to come from a trusted publisher. | High |
| Embedding Model | Tampering | Embedding model modification | A modified embedding model could change how documents or queries are represented and affect retrieval results. | High |
| Embedding Model | Repudiation | Missing model provenance | Without records of the model source, version, and integrity, it may be difficult to identify unauthorized model changes. | Medium |
| Embedding Model | Information Disclosure | Sensitive embedding exposure | Embeddings may contain information derived from sensitive documents and could be exposed through insecure storage or logging. | High |
| Embedding Model | Denial of Service | Embedding resource exhaustion | Excessive document or query processing could consume the resources required to generate embeddings. | Medium |
| Embedding Model | Elevation of Privilege | Unauthorized model replacement | An attacker with access to the model supply chain could replace the trusted embedding model and influence retrieval behavior. | High |
| Vector Database | Spoofing | Unauthorized database identity | An attacker could impersonate an authorized service or tenant when querying the vector database. | High |
| Vector Database | Tampering | Metadata or vector modification | Unauthorized changes to vectors or tenant metadata could cause incorrect or cross-tenant retrieval. | High |
| Vector Database | Repudiation | Missing query and change logs | Without sufficient database audit logs, unauthorized queries or changes may be difficult to investigate. | Medium |
| Vector Database | Information Disclosure | Cross-tenant retrieval | Weak tenant isolation could allow one customer to retrieve another customer's documents from the shared vector index. | Critical |
| Vector Database | Denial of Service | Retrieval flooding | Excessive vector searches could consume database and compute resources and affect legitimate users. | Medium |
| Vector Database | Elevation of Privilege | Direct database access | An attacker who bypasses application controls could access data or perform queries outside their authorized tenant. | High |
| AI Model / RAG Assistant | Spoofing | Untrusted retrieved context | Malicious or untrusted content could be presented to the AI model as trusted retrieved context. | High |
| AI Model / RAG Assistant | Tampering | Prompt or context manipulation | Retrieved content or user input could manipulate the model's instructions or influence its response. | High |
| AI Model / RAG Assistant | Repudiation | Untraceable AI decisions | Without request, retrieval, and response logs, it may be difficult to investigate how a sensitive response was produced. | Medium |
| AI Model / RAG Assistant | Information Disclosure | Sensitive information in responses | The assistant could reveal confidential information retrieved from documents that the user should not have access to. | Critical |
| AI Model / RAG Assistant | Denial of Service | Resource exhaustion | Excessive or complex prompts and retrieval requests could consume model and retrieval resources. | Medium |
| AI Model / RAG Assistant | Elevation of Privilege | Prompt-based security bypass | Carefully crafted prompts or retrieved content could attempt to bypass application restrictions and influence access to protected information. | High |
| Knowledge Base | Spoofing | Fake document identity | An attacker could make a malicious document appear to be an approved PayGuard knowledge-base document. | High |
| Knowledge Base | Tampering | Knowledge-base poisoning | Malicious content inserted into the knowledge base could change what the RAG system retrieves and presents to the model. | High |
| Knowledge Base | Repudiation | Missing document history | Without document provenance and version history, it may be difficult to determine who added or changed a document. | Medium |
| Knowledge Base | Information Disclosure | Unauthorized document retrieval | Sensitive documents could be returned to a user or tenant that should not have access to them. | Critical |
| Knowledge Base | Denial of Service | Knowledge-base flooding | Excessive malicious documents could increase storage and retrieval workload and affect normal operation. | Medium |
| Knowledge Base | Elevation of Privilege | Unauthorized knowledge-base access | An attacker with unauthorized write or administrative access could influence information presented by the RAG system. | High |
| Fine-Tuning Pipeline | Spoofing | Training pipeline identity spoofing | An attacker could impersonate a trusted training job or pipeline component and introduce unauthorized training activity. | High |
| Fine-Tuning Pipeline | Tampering | Training data or configuration tampering | An attacker could modify training data, model configuration, or training parameters before the model is updated. | Critical |
| Fine-Tuning Pipeline | Repudiation | Missing training audit trail | Without reliable records of training inputs, configuration changes, and job execution, it may be difficult to determine who changed the model or when. | Medium |
| Fine-Tuning Pipeline | Information Disclosure | Training data exposure | Sensitive or tenant-specific data used during fine-tuning could be exposed through logs, artifacts, checkpoints, or the resulting model. | High |
| Fine-Tuning Pipeline | Denial of Service | Training resource exhaustion | An attacker could submit excessive or malicious training jobs that consume compute resources and prevent legitimate model updates. | Medium |
| Fine-Tuning Pipeline | Elevation of Privilege | Unauthorized model modification | An attacker with excessive pipeline permissions could modify the training process and gain influence over the resulting model. | Critical |

---

## Key PayGuard Findings

### 1. Cross-Tenant Retrieval

The Week 10 assessment demonstrated that tenant isolation was being enforced at the application layer rather than at the database layer.

The normal flow was:

```text
User → Application Filter → Vector Database

