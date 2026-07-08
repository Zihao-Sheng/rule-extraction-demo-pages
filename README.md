# Green Metrics Technology Rule Extraction Demos

This repository is a static demonstration package for the Green Metrics Technology capstone project. The main collaboration repository is private, so this repo keeps only the presentation-ready artifacts needed to explain and show the rule extraction workflow.

The demos visualize how zoning bylaw PDFs are converted into page blocks, selected as Graph/RAG evidence packs, and prepared for LLM-based rule extraction.

## Demo Pages

- `index.html` - landing page with links to the city demos
- `rag_pipeline_transfer_animation.html` - Burnaby demo
- `calgary_rag_pipeline_transfer_animation.html` - Calgary demo

Open `index.html` in a browser, or deploy this folder as a static site. Keep the HTML files, JS data files, image crop folders, and `vendor/` folder together.

## What This Repo Shows

- A compact Burnaby example for R1 small-scale multi-unit housing.
- A larger Calgary example for backyard suite / secondary suite retrieval.
- Raw visual block crops from selected bylaw pages.
- Stage 2 page reconstruction previews with layout boxes.
- Interactive Graph/RAG pack visualizations built from pipeline output.
- A final presentation layer showing how evidence blocks transfer toward rule extraction outputs.

## Pipeline Summary

The full private pipeline works in six broad stages:

1. PDF page processing and visual block extraction.
2. Local candidate selection around target zoning terms.
3. Auto-discovery of nearby sections, rule signals, cross-references, and supporting context.
4. Semantic compression using structural signals, lexical scoring, and optional embedding scores.
5. Graph/RAG pack construction, grouped into lanes such as core rules, use permissions, district dimensional overrides, and universal applicable rules.
6. Batched LLM rule extraction, with separate handling for text blocks and table-like visual regions.

For more project and pipeline context, see [`PROJECT_CONTEXT.md`](./PROJECT_CONTEXT.md).

## Repository Role

This is not the production pipeline repository. It is a shareable static demo that preserves the key story of the work without exposing the private capstone repo, API configuration, or full source/output tree.
