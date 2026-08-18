# Project 3 Green Audit Lab

This repository contains my submission for the **Project 3 Green Audit Lab**, completed as part of the AI Consulting Bootcamp.

## Project Overview

The audited system is **Project 3 – VR Competitive Intelligence Assistant**, an AI-powered application that automates competitive research for the vacation rental industry.

The system accepts a user query, retrieves relevant competitor documentation through vector search and live retrieval, synthesizes the results using Large Language Models (LLMs), and publishes a structured competitive intelligence report to Notion while returning the final response through Slack.

The purpose of this audit is **not** to produce a certified carbon footprint. Instead, it applies Green Software Foundation (GSF) principles and the Software Carbon Intensity (SCI) framework to identify where compute is concentrated, evaluate sustainability trade-offs, and recommend practical engineering improvements based on measured observations.

---

# Repository Contents

| File                                     | Description                                                                                                                                                                                              |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **project3-green-compute-brief.md** | Technical overview of the system's operational footprint, execution flow, compute components, hosting assumptions, and measured runtime characteristics.                                                 |
| **project3-green-audit.md**         | Complete sustainability assessment following the lab phases, including the compute map, SCI analysis, hotspot identification, Green Software Foundation pillar mapping, and improvement recommendations. |
| **project3-sustainability-memo.md** | Client-facing sustainability memo summarizing the audit findings, recommended actions, limitations, and next steps for the engineering team.                                                             |
| **README.md**                            | Repository overview and file guide.                                                                                                                                                                      |

---

# Measurement Methodology

The original Project 3 implementation did **not** record operational metrics such as token usage, API calls, cache performance, or execution time. To produce a more evidence-based sustainability assessment, a **temporary local instrumentation layer** was added during the preparation of this lab.

These changes were made **only on a local copy of the project** and were **not committed to the original Project 3 repository**. Their sole purpose was to observe runtime characteristics (for example, token consumption, cache behaviour, and execution time) without altering the system's functionality or architecture.

The audited implementation therefore remains functionally identical to the submitted Project 3. The instrumentation simply exposed metrics that were previously unavailable, allowing this audit to rely on measured observations instead of engineering estimates wherever possible.

---

# Assessment Scope

The audit evaluates the system across the following areas:

* Operational compute flow
* Functional Unit (R)
* System compute map
* Software Carbon Intensity (SCI) framework
* Operational (O) and embodied (M) emissions considerations
* Compute hotspot identification
* Green Software Foundation (GSF) pillars
* Sustainability improvement opportunities
* Measurement strategy and engineering recommendations

Where possible, findings are based on **measured execution data** collected from an instrumented pipeline run, including LLM API calls, token usage, cache performance, and runtime metrics. Remaining unknowns are explicitly identified rather than estimated.

---

# Key Findings

The audit identified three primary areas contributing to the system's operational footprint:

1. **LLM inference**, which represents the largest source of computational work during report generation.
2. **Live retrieval and prompt construction**, which contribute substantially to the overall input context processed by the model.
3. **Oversized prompt payloads**, where duplicated filesystem metadata (`rag_documents`) accounted for approximately 40% of measured input tokens despite providing no semantic value to the language model.

The assessment also identified practical opportunities to reduce operational compute through payload optimization, response caching, model routing, and improved telemetry.

---

# Notes

This assessment should **not** be interpreted as a formal carbon accounting exercise or a certified Software Carbon Intensity (SCI) report.

Several important sustainability metrics—such as provider energy consumption, hardware lifecycle emissions, infrastructure-level carbon intensity, and model-specific energy consumption—remain inaccessible when using third-party AI APIs. Where direct measurement was not possible, assumptions are clearly labelled, and recommendations focus on measurable engineering improvements rather than unsupported environmental claims.

The measured runtime metrics included in this repository should therefore be understood as an engineering baseline for comparison and optimization, rather than as a complete environmental impact assessment.

