# Project 3 Green Compute Brief

## System Overview

This document describes the operational compute footprint of **Project 3 – VR Competitive Intelligence Assistant**. It is intended as a technical overview for sustainability analysis and follows the Green Software Foundation (GSF) approach of understanding **what the system actually computes** before assessing its environmental impact.

The application assists Product Managers by automating competitive intelligence research across Property Management Systems (PMS) in the vacation rental industry. A user submits a question through Slack, the application retrieves and analyzes relevant competitor documentation, generates a structured comparison using a Large Language Model (LLM), stores the results in Notion, and returns the final report to the user.

The system combines Retrieval-Augmented Generation (RAG), vector search, live web retrieval, and LLM inference. Most operational compute occurs during retrieval and report generation rather than during data preparation.

---

# End-to-End Execution Flow

For a typical user request, the system performs the following sequence of operations:

1. **User request**

   * A competitive intelligence question is submitted through Slack.

2. **Question analysis**

   * The first LLM completion call classifies the request and determines the retrieval strategy.

3. **Runtime query embeddings**

   * The application generates runtime embeddings for the search queries.
   * These embeddings are reused across all competitors during the same execution through an in-memory cache.

4. **Vector retrieval**

   * Relevant documentation is retrieved from the existing vector database.

5. **Live retrieval**

   * When required, Firecrawl and Tavily retrieve up-to-date documentation from external sources.

6. **Report synthesis**

   * A second LLM completion call combines all retrieved evidence into a structured competitive intelligence report.

7. **Report publication**

   * The report is written to Notion and delivered back to the requesting user through Slack.

---

# LLM API Usage

A measured execution of the current implementation produced the following values.

| Metric                      |  Observed Value |
| --------------------------- | --------------: |
| Completion API calls        |           **2** |
| Runtime embedding API calls |           **7** |
| Input tokens                |     **217,393** |
| Output tokens               |       **2,745** |
| Embedding tokens            |          **64** |
| Pipeline runtime            | **637 seconds** |

The application always performs two completion requests during a report:

* Question analysis
* Final report generation

Runtime embedding calls depend on the number of unique search queries generated during execution. A typical report performs six to seven embedding requests.

---

# Embeddings and Retrieval

The system uses embeddings in two different ways.

## Offline corpus embeddings

The competitor knowledge base is embedded offline using an incremental pipeline.

Characteristics:

* embeddings are generated only when source content changes
* unchanged documents are skipped using content hashes
* embedding requests are batched
* no embeddings are generated during normal report execution for the existing corpus

This minimizes repeated preprocessing work.

---

## Runtime query embeddings

Each report generates embeddings only for the search queries required during that execution.

Measured cache statistics:

* Query embedding cache hits: **29**
* Query embedding cache misses: **7**
* Cache hit rate: **81%**

Each unique query embedding is computed once and reused across all competitors during the same report generation.

---

# Batch Processing and Model Training

The system **does not perform model training or fine-tuning**.

Background compute is limited to:

* incremental corpus embedding
* vector index updates when documentation changes

These maintenance tasks are manually triggered and are not part of the normal user workflow.

---

# Hosting Assumptions

The current deployment architecture consists of several independent services.

| Component           | Region / Location                              |
| ------------------- | ---------------------------------------------- |
| Application runtime | Local development machine (Spain)              |
| OpenAI API          | Third-party cloud provider (assumed US-hosted) |
| Firecrawl           | Cloud service (US)                             |
| Tavily              | Cloud service (US)                             |
| Notion              | Cloud service (US)                             |
| Slack               | Region not verified                            |

These hosting locations are based on the current development environment and publicly available service information where applicable. Infrastructure details for third-party providers are not fully accessible.

---

# Caching Behaviour

The application implements caching selectively.

### Present

* Incremental corpus embeddings
* Runtime query embedding reuse
* Existing vector database reused across executions

### Not Present

* Response cache for identical user questions
* Retrieval result cache
* LLM completion cache

As a consequence, repeated user requests execute the full retrieval and inference pipeline each time.

---

# Operational Compute Footprint

The measured execution indicates that operational compute is concentrated in three primary areas.

## 1. LLM inference

Two completion calls account for the largest share of inference workload during each report.

## 2. Retrieval pipeline

Vector retrieval, live web retrieval, and evidence preparation generate substantial network activity and contribute significantly to the final prompt size.

## 3. Prompt construction

Instrumentation revealed that approximately **217,000 input tokens** were submitted during one representative execution.

Further inspection identified a significant optimisation opportunity: approximately **40% of the prompt consisted of duplicated filesystem metadata (`rag_documents`)** that was serialized multiple times but provided no meaningful information for model reasoning.

This finding represents the largest identified opportunity to reduce operational compute without affecting report quality.

---

# Summary

The current architecture already incorporates several efficient engineering practices, including incremental corpus embeddings and runtime embedding reuse. However, the measured execution demonstrates that the majority of operational compute is concentrated in LLM inference and prompt construction rather than embedding generation itself.

The instrumented baseline established in this compute brief provides the foundation for the accompanying sustainability audit and allows future optimisation work to be evaluated against measured system behaviour rather than assumptions alone.
