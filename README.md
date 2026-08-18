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
| **Marc-project3-green-compute-brief.md** | Technical overview of the system's operational footprint, execution flow, compute components, hosting assumptions, and measured runtime characteristics.                                                 |
| **Marc-project3-green-audit.md**         | Complete sustainability assessment following the lab phases, including the compute map, SCI analysis, hotspot identification, Green Software Foundation pillar mapping, and improvement recommendations. |
| **Marc-project3-sustainability-memo.md** | Client-facing sustainability memo summarizing the audit findings, recommended actions, limitations, and next steps for the engineering team.                                                             |
| **README.md**                            | Repository overview and file guide.                                                                                                                                                                      |

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
2. **Live retrieval and retrieval context construction**, which increase network activity and contribute substantially to prompt size.
3. **Oversized prompt payloads**, where duplicated filesystem metadata significantly increases input token volume without improving model performance.

The assessment also found opportunities to reduce operational compute through response caching, payload optimization, model routing, and improved telemetry.

---

# Notes

This assessment should **not** be interpreted as a formal carbon accounting exercise or a certified Software Carbon Intensity (SCI) report.

Several important sustainability metrics—such as provider energy consumption, hardware lifecycle emissions, and infrastructure-level carbon intensity—are not available through third-party AI APIs. Where direct measurement was not possible, assumptions are clearly labelled, and recommendations focus on measurable engineering improvements rather than unsupported environmental claims.
