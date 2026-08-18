# Sustainability Assessment Memo

**To:** Operations & Engineering Lead
**Project:** VR Competitive Intelligence Assistant
**Subject:** Green Software Assessment and Recommended Next Steps

## Executive Summary

The current sustainability footprint of the VR Competitive Intelligence Assistant should be considered **a moderate concern rather than a critical issue**. The system already incorporates several efficient engineering practices, including incremental corpus embeddings and runtime query embedding reuse, which avoid unnecessary repeated computation. However, the audit identified opportunities to significantly reduce operational compute during report generation without affecting functionality or report quality.

The highest-priority recommendation is to **remove duplicated filesystem metadata (`rag_documents`) from the LLM synthesis payload**. Measured execution showed that approximately **217,000 input tokens** were processed during a representative report generation. Further analysis determined that roughly **40% of those tokens consisted of duplicated file path metadata that provides no semantic value to the language model**. Eliminating this unnecessary payload is expected to substantially reduce inference compute, API cost, latency, and the associated operational footprint with minimal implementation effort.

The second recommendation is to **implement response caching for repeated or identical competitive intelligence requests**. At present, identical questions trigger the full retrieval and inference pipeline every time, resulting in repeated LLM calls, retrieval operations, and network activity. Returning previously generated reports where appropriate would reduce unnecessary computation while improving response times.

The third recommendation is to **instrument operational sustainability metrics**, including token usage, API calls, cache hit rates, retrieval size, and latency. These measurements would establish a reliable baseline for future Software Carbon Intensity (SCI) comparisons and allow engineering decisions to be based on observed evidence rather than estimates.

Several important sustainability metrics remain unavailable. The application cannot access provider-level energy consumption, infrastructure carbon intensity, or hardware lifecycle emissions for the third-party AI services it depends upon. Consequently, this assessment should not be interpreted as a certified carbon footprint or formal SCI calculation. Instead, it provides a practical engineering assessment that identifies measurable opportunities to reduce unnecessary computation and improve the sustainability of the current architecture through targeted, evidence-based optimisation.
