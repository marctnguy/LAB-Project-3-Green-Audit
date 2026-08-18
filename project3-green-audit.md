# Project 3 Green Audit

## Green Software Foundation (GSF) Sustainability Assessment

### Project

**VR Competitive Intelligence Assistant**

---

# Introduction

This audit evaluates the sustainability characteristics of **Project 3 – VR Competitive Intelligence Assistant** using the Green Software Foundation (GSF) principles and the Software Carbon Intensity (SCI) framework.

The objective is **not** to calculate an exact carbon footprint. Instead, the audit identifies where compute is concentrated, distinguishes measured observations from assumptions, and recommends practical engineering improvements that reduce unnecessary computation while maintaining report quality.

Where measurements were available, they have been used as the basis for this assessment. Where information was unavailable through third-party AI providers, uncertainties are explicitly identified rather than estimated.

---

# Phase 1 – System Compute Map

## Operational Compute Components

| Component                | Triggered By                                  | Estimated Frequency per Report | Purpose                                                   |
| ------------------------ | --------------------------------------------- | -----------------------------: | --------------------------------------------------------- |
| LLM Question Analysis    | Every user request                            |                              1 | Classifies the request and determines retrieval strategy  |
| LLM Report Synthesis     | Every user request                            |                              1 | Generates the final competitive intelligence report       |
| Runtime Query Embeddings | Every user request                            |                            6–7 | Generates embeddings for search queries                   |
| Vector Retrieval         | Every user request                            |                       Multiple | Retrieves relevant documentation from the vector database |
| Firecrawl Retrieval      | When fresh documentation is required          |                       Variable | Retrieves up-to-date documentation                        |
| Tavily Search            | When additional external evidence is required |                       Variable | Supplements retrieved information                         |
| Notion Write             | Every completed report                        |                              1 | Stores the generated report                               |
| Slack Response           | Every completed report                        |                              1 | Returns results to the user                               |

---

## Measured LLM Activity

An instrumented execution of the current implementation produced the following observations.

| Metric                         |               Measured Value |
| ------------------------------ | ---------------------------: |
| Completion API calls           |                        **2** |
| Runtime embedding calls        |                        **7** |
| Input tokens                   |                  **217,393** |
| Output tokens                  |                    **2,745** |
| Embedding tokens               |                       **64** |
| Query embedding cache hit rate | **81% (29 hits / 7 misses)** |
| Runtime                        |              **637 seconds** |

Unlike the original architectural estimate, these values represent measurements collected from an instrumented execution and therefore establish a reliable operational baseline.

---

## Token Estimates

The application does not currently log token usage during normal operation.

For this audit, measured values obtained from the instrumented execution replace earlier engineering estimates.

However, several values remain unavailable:

* completion API pricing for the configured model
* provider energy consumption
* infrastructure utilisation
* hardware lifecycle emissions

These limitations prevent precise carbon accounting.

---

# Phase 2 – Functional Unit and SCI Assessment

## Functional Unit (R)

**One functional unit (R) is:**

> One completed competitive intelligence report generated in response to a user query.

The functional unit includes the entire execution pipeline, from receiving the user request to publishing the final report through Slack and Notion.

---

## Operational Emissions (O)

Operational emissions are expected to be dominated by cloud inference and retrieval rather than local computation.

The measured execution demonstrates:

* two completion requests
* seven embedding requests
* more than 217,000 input tokens
* multiple retrieval operations
* over ten minutes of total execution time

Because provider energy consumption is unavailable, API usage serves only as a directional proxy for operational compute.

Completion pricing for the configured model could not be verified at the time of this audit, preventing a reliable cost-per-report estimate.

Embedding costs are negligible by comparison and therefore are unlikely to represent the primary operational contributor.

---

## Embodied Emissions (M)

Embodied emissions cannot be calculated for API-based inference.

The hardware operating OpenAI, Firecrawl, Tavily, Notion, and Slack is owned and managed by third-party providers.

Consequently:

> Embodied emissions are outside the visibility of this application and cannot be incorporated into an SCI calculation without provider-level lifecycle data.

This represents an unavoidable limitation of API-based AI systems.

---

## What this SCI assessment can and cannot say

### This assessment can:

* identify compute hotspots
* compare architectural alternatives
* prioritise optimisation opportunities
* establish a measured operational baseline

### This assessment cannot:

* calculate precise gCO₂eq per report
* determine provider electricity consumption
* estimate embodied hardware emissions
* certify overall environmental impact

---

# Phase 3 – Hotspot Analysis

## Hotspot 1 – Oversized Prompt Construction

This audit identified the largest optimisation opportunity within the current implementation.

Although the measured execution consumed **217,393 input tokens**, further inspection showed that approximately **40% of the prompt consisted of duplicated filesystem metadata (`rag_documents`)**.

These paths were serialized multiple times despite providing no useful semantic information to the language model.

### Opportunity

Removing this duplicated payload could reduce prompt size by approximately **40%** while preserving report quality.

Expected benefits include:

* fewer processed tokens
* reduced inference compute
* lower API costs
* lower operational emissions
* reduced latency

This optimisation requires only an application-level change and no external infrastructure.

---

## Hotspot 2 – LLM Inference

Every report performs two completion requests:

* request analysis
* report synthesis

Inference remains the largest consumer of computational resources during normal operation.

### Current observations

Question analysis is considerably simpler than report synthesis but currently uses the same model.

### Opportunity

Introduce model routing so that lightweight orchestration tasks use a smaller model while reserving the larger model for report generation.

---

## Hotspot 3 – Live Retrieval

The retrieval pipeline combines:

* vector search
* Firecrawl
* Tavily

Fresh retrieval increases network traffic and expands the context supplied to the synthesis model.

Some documentation changes infrequently, making repeated retrieval unnecessary for identical or near-identical requests.

### Opportunity

Introduce freshness windows and retrieval caching where appropriate.

---

# Phase 4 – Lever Recommendations

| Lever                     | Proposed Change                                                                        | GSF Pillars                                       | Expected Impact                                                | Trade-Off                                     |
| ------------------------- | -------------------------------------------------------------------------------------- | ------------------------------------------------- | -------------------------------------------------------------- | --------------------------------------------- |
| Remove duplicated payload | Exclude repeated `rag_documents` metadata from synthesis payload                       | Carbon Efficiency, Energy Efficiency              | Significant reduction in processed input tokens                | Minor implementation effort                   |
| Response caching          | Return existing reports for repeated or identical requests                             | Carbon Efficiency, Energy Efficiency, Measurement | Lower inference workload and lower latency                     | Cache invalidation strategy required          |
| Model routing             | Use a smaller model for question analysis while keeping the larger model for synthesis | Carbon Efficiency, Energy Efficiency              | Reduced inference cost and operational compute                 | Requires evaluation of classification quality |
| Sustainability telemetry  | Record token usage, API calls, cache hits, latency and retrieval size                  | Measurement                                       | Enables evidence-based optimisation and future SCI assessments | Additional logging and monitoring effort      |

At least two recommendations can be implemented entirely within the application without requiring external infrastructure changes.

---

# Verification – Honesty Check

This audit distinguishes clearly between observations, estimates, and unknowns.

## Measured

* Completion API calls
* Runtime embedding calls
* Token usage
* Runtime duration
* Query embedding cache performance

## Estimated

* Reduction in prompt size following payload optimisation
* Operational impact of model routing

## Unknown

* Provider energy consumption
* Completion API pricing
* Hardware lifecycle emissions
* Infrastructure carbon intensity

No unsupported environmental claims are made.

---

# Overall Assessment

The system already demonstrates several efficient engineering practices, including incremental corpus embeddings, runtime embedding reuse, and an 81% query embedding cache hit rate.

However, the audit identified one unexpectedly large source of unnecessary computation: duplicated filesystem metadata included in the synthesis payload. This single architectural issue accounts for approximately 40% of measured input tokens while providing no additional reasoning value to the language model.

Combined with response caching, retrieval optimisation, and model routing, these recommendations provide a clear roadmap for reducing operational compute without compromising the quality of generated competitive intelligence reports.

Rather than attempting to estimate an unverifiable carbon footprint, this audit establishes a measured baseline from which future engineering improvements can be objectively evaluated using Green Software Foundation principles.
