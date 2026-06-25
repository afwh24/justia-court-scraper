# U.S. Federal District Court Legal Data Acquisition Pipeline

A scalable legal document acquisition pipeline for scraping, downloading, and structuring U.S. Federal District Court opinions from [Justia Law](https://law.justia.com/cases/) into machine-readable datasets for legal AI and NLP research.

---

## Overview

This project automates the large-scale collection of U.S. Federal District Court case records from Justia Law. The pipeline crawls court records across a four-level navigation hierarchy, extracts structured case information from both PDF and HTML sources, and produces metadata-rich JSONL datasets for downstream legal AI applications.

The extracted legal documents can be used for:

- Legal NLP research
- LLM training and fine-tuning
- Retrieval-Augmented Generation (RAG)
- Legal document retrieval and search
- Legal AI dataset preparation

---

## Pipeline Architecture

1. Crawl Justia Law across a four-level navigation hierarchy: State to Year to Court to Case
2. Extract case metadata from each case page
3. Download opinion PDF if available for download
4. If PDF is not available, extract HTML text content instead
5. Store files using URL-based hashed filenames
6. Structure extracted information into JSONL records
7. Save outputs with error logging

---

## Features

### Hierarchical Court Scraping
- Four-level navigation crawling: State to Year to Court to Case
- Automated scraping of U.S. Federal District Court opinions from Justia Law
- Case metadata extraction from each page level
- PDF download automation where PDFs are available for download
- HTML text extraction as fallback where PDFs are unavailable
- URL-based hashed file storage for organised and reproducible output

### HTML Processing
- BeautifulSoup-based HTML parsing and text extraction
- Structured text cleaning and normalisation

### Dataset Generation
- JSONL dataset outputs
- Metadata-rich legal records
- Reproducible document organisation

### Fault Tolerance
- Retry logic with exponential backoff
- Duplicate detection to avoid reprocessing
- Resume support for interrupted runs
- Robust error handling for HTTP 403, 410, and 503 errors, TLS failures, read timeouts, and inconsistent web page structures

---

## Dataset Schema

Each JSONL record contains the following fields:

```json
{
  "url": "https://law.justia.com/cases/...",
  "title": "United States v. Example",
  "text": "Full extracted text of the court opinion..."
}
```

---

## Technical Challenges

- **HTTP 403, 410, and 503 responses** — Handled via retry logic and exponential backoff
- **TLS and connection failures** — Managed with configurable retry thresholds and error recovery
- **Read timeouts** — Handled with timeout detection and automatic retries
- **Missing or inconsistent web page structures** — Managed through robust error handling and graceful degradation
- **Duplicate records** — Detected and skipped via duplicate detection mechanisms
- **Interrupted runs** — Supported via resume capabilities without reprocessing completed records

---

## Technologies Used

- **Language:** Python
- **HTML Parsing:** BeautifulSoup
- **HTTP Requests:** Requests
- **Output Format:** JSONL
- **Source:** [Justia Law — Federal Cases](https://law.justia.com/cases/)

---

## Applications

- Legal AI systems
- Court opinion analysis
- Legal document retrieval
- LLM training datasets
- Legal AI research

---

## Notes

This pipeline was built during an AI Engineer internship at the Home Team Science and Technology Agency (HTX), Singapore. Extracted datasets are not publicly available.
