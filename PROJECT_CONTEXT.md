# Project Context

## Green Metrics Technology Capstone

The Green Metrics Technology capstone explored how municipal zoning bylaws can be converted into structured, checkable rules. The broader product direction was to help evaluate whether a proposed residential building is likely feasible on a given parcel by combining:

- municipal zoning bylaw text,
- zoning district and parcel context,
- extracted rule values such as setbacks, coverage, parking, height, and use permissions,
- and a downstream code-based checker that can compare extracted constraints against a proposed design.

The project narrowed the initial prototype around explainable zoning rule retrieval and extraction. Instead of asking a language model to read an entire long bylaw PDF directly, the pipeline first finds and packages the most relevant evidence, then sends bounded batches to an LLM for structured extraction.

## Why Retrieval Matters

Municipal bylaws are long, inconsistent, and visually messy. Important rules may appear in tables, footnotes, use-specific sections, district-specific sections, and general regulations that apply by cross-reference. A direct PDF-to-LLM approach is fragile because the model may miss a relevant exception or read the wrong district.

The rule extraction work therefore uses a retrieval-first architecture:

1. Break the bylaw into visual and textual blocks.
2. Score blocks for relevance to the target use or district.
3. Preserve surrounding context and cross-references where needed.
4. Build Graph/RAG packs that explain why each block was selected.
5. Extract structured rules only from the selected evidence.

## Rule Extraction Pipeline

The final rule extraction pipeline represented in these demos is the `official_rule_extraction_pipeline_batched_graph_rag_rule_extraction` workflow. It combines document layout processing, graph-based evidence grouping, RAG-style retrieval, and batched LLM extraction.

### 1. PDF to Visual Blocks

The pipeline starts from municipal zoning bylaw PDFs. Pages are rendered and segmented into local visual blocks, preserving:

- page number,
- local block id,
- block text,
- bounding boxes,
- table-like regions,
- and crop images for inspection.

The demo repo includes selected crop images in:

- `burnaby_raw_block_crops/`
- `calgary_raw_block_crops/`
- `stage2_page_previews/`

These assets let the presentation show the pipeline at the evidence level, not just as final JSON.

### 2. Candidate Selection and Discovery

The private pipeline selects candidate blocks using target terms and parent terms. Example targets include:

- Burnaby: `rear principal building`, `front principal building`, `small-scale multi-unit`
- Calgary: `backyard suite`, `secondary suite`

The selection step also tracks broader context terms such as lot, yard, building, dwelling, parking, parcel, use, and separation. It keeps target hits, nearby context, inherited section context, and generic rules that may apply across a district.

### 3. Semantic Compression

After discovery, the pipeline compresses the evidence set so the extraction model sees less noise. The scoring policy combines:

- structural score,
- lexical score,
- embedding score where available,
- and lane or role bonuses.

In the BGE-M3 experiment branch, embeddings were generated through local Ollama embedding cache files. The official demo artifacts emphasize the selected packs and extraction-ready evidence rather than requiring the embedding service at demo time.

### 4. Graph/RAG Pack Construction

Selected blocks are grouped into Graph/RAG packs. A pack is a small evidence bundle with an applicability lane and a reason for inclusion. The main lanes shown in the demo data are:

- `core_rule`
- `use_permission`
- `district_dimensional_override`
- `universal_applicable_rule`
- `table_extraction_package`

Each pack links back to source evidence blocks, original page numbers, roles, char counts, and target-filter actions such as `kept_target_hit`, `kept_generic_applicable`, `kept_inherited_target_context`, or `trimmed_to_target`.

This graph structure is what the 3D demo pages visualize.

### 5. Batched Rule Extraction

The extraction stage converts the selected evidence into structured rule records. It separates text extraction from table extraction because table regions need different handling than prose blocks.

The official pipeline supports:

- pack-adaptive batching,
- text and table model selection,
- preflight skipping of unsuitable blocks,
- raw API response capture,
- parsed JSON outputs,
- post-processing filters,
- normalized merge audits,
- and deduplicated rule output.

The recorded pipeline summaries show LLM extraction configured with Gemini models such as `gemini-2.5-flash` and `gemini-3.5-flash`. The static demo does not call those APIs; it only displays the prepared and exported artifacts.

## Demonstrated City Cases

### Burnaby

The Burnaby demo is the compact case. It focuses on R1 small-scale multi-unit housing and shows a smaller evidence graph. The demo data includes raw block crops, RAG packs, evidence blocks, a table extraction package, and final visualization states.

The public demo graph includes:

- 12 displayed packs in the presentation graph,
- 18 displayed evidence blocks,
- 4 table regions,
- lanes for dimensional standards, use permissions, parking, and target context.

### Calgary

The Calgary demo is the larger case. It shows how the same pipeline scales to a large bylaw and a larger selected evidence set for backyard suite and secondary suite rules.

The official adapter summary records:

- 130 RAG packs,
- 178 text blocks,
- 1 table region,
- 130 pseudo-pages,
- target filtering across broad context, target hits, inherited context, and use-permission filtering.

The landing page highlights 100 raw block previews from a much larger local block set, then connects those previews to the full official Calgary RAG graph.

## Technology Used

The private project used Python for the pipeline and static HTML/JavaScript for the demo layer. The core techniques include:

- PDF rendering and page/block segmentation,
- visual block crop generation,
- rule-signal heuristics,
- target-term and parent-term retrieval,
- structural and lexical scoring,
- optional local embedding scoring with BGE-M3 through Ollama in the experiment branch,
- graph-style evidence grouping,
- batched LLM extraction with Gemini models,
- JSON/JSONL/CSV output artifacts for audits and downstream use,
- and browser-based visualization with Three.js and 3D Force Graph.

This repository vendors the browser visualization dependencies under `vendor/` so the demos can run as static pages without installing the full private pipeline.

## Meaning of This Repository

This repo is the public-facing demonstration layer for the rule extraction work. It is useful for:

- showing the end-to-end story during a final presentation,
- explaining why Graph/RAG retrieval was used before extraction,
- inspecting selected evidence visually,
- demonstrating how raw bylaw pages become rule extraction inputs,
- and sharing the work without exposing the private collaboration repository.

It intentionally excludes the full private source tree, API keys, full municipal PDF collection, and large intermediate output folders.
