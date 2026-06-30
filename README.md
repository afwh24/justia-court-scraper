# Justia U.S. Federal District Court Scraper

A large-scale hierarchical web scraping pipeline that collects U.S. Federal District Court case documents from [Justia Law](https://law.justia.com) for legal AI research. Built during a six-month AI Engineer internship at HTX, Singapore's national defence-tech agency.

## Scale

Over **860,000 U.S. Federal District Court cases** collected across all fifty U.S. states and the District of Columbia.

## How it works

### Navigation

The scraper follows a four-level hierarchy:

```
State → Year → Court → Case
```

For each state, the scraper discovers available years, then within each year discovers courts, then within each court discovers individual case listings, following pagination links where a court has more cases than fit on a single listing page.

### Extraction

For each case page, the scraper checks whether a downloadable PDF opinion is available:

- **PDF available** → downloaded directly as the primary method
- **No PDF available** → case text extracted directly from the HTML, using a two-tier fallback to locate the relevant content section (page layouts are not fully consistent across the site)

Each text-extracted case is written to a JSONL file (one file per state-year combination) with three fields: `url`, `title`, `text`.

### Deduplication

Two separate mechanisms handle deduplication for the two output types:

- **JSONL case records**: the set of case URLs already present in a given state-year's output file is loaded into memory at the start of processing that year; any URL already in the set is skipped
- **PDF downloads**: each PDF's filename is derived from a hash of its source URL, and downloads are skipped if a file already exists at that path

### Resilience

- Retry logic with **exponential backoff** on transient failures
- Explicit handling for HTTP 410 (resource permanently removed) and HTTP 404 (invalid URL) — both logged and skipped without retrying
- Read timeouts explicitly caught and retried with the same backoff strategy
- All other failures, after exhausting retries, are logged with a timestamp to an error file; the scraper continues processing rather than terminating

### Parallelism

Given the scale of the task, the workload was manually partitioned by U.S. state/district into separate groups, allowing different subsets to be processed in parallel across separate runs rather than relying on multithreading or async concurrency within a single process.

### Post-processing

- Empty JSONL files (state-year combinations with zero valid records) are automatically deleted
- A separate utility reorganises completed output per state: JSONL files are moved into a dedicated `jsonl/` subfolder, PDF files into a dedicated `pdf/` subfolder (with filenames prefixed by year to avoid collisions across years), and the now-empty year-level folders are removed

## Tech stack

Python, requests, BeautifulSoup, JSONL

## Output structure

```
output/
└── <State>/
    ├── jsonl/
    │   └── _<Year>.jsonl
    └── pdf/
        └── <Year>_<hash>.pdf
```
