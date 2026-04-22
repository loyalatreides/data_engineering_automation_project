# Data Engineering Automation Project

An end-to-end intelligent document processing (IDP) pipeline built on Databricks that automatically ingests PDF financial documents, classifies them using AI, and extracts structured data into Delta tables.

## Overview

Raw PDF files (invoices, purchase orders, and receipts) are uploaded to a Databricks Volume. A Databricks Job monitors the volume and triggers the pipeline automatically whenever a new file arrives. The notebook then parses, classifies, and extracts structured data from each document using Databricks AI Functions.

## Pipeline Architecture

```
Raw PDFs (Volume)
      │
      ▼
1. Parse Documents      →  ai_parse_document()  →  parsed_raw_documents
      │
      ▼
2. Classify Documents   →  ai_classify()        →  classified_files
      │
      ├──► Invoice       →  ai_extract()  →  invoices table
      ├──► Purchase Order →  ai_extract()  →  purchase_orders table
      └──► Receipt        →  ai_extract()  →  receipts table
```

## Steps

### 1. Document Parsing
All PDF files are read from `/Volumes/workspace/idp/final_project` using `read_files()`. The `ai_parse_document()` function extracts the raw content from each PDF and stores it in the `workspace.idp.parsed_raw_documents` table.

### 2. Document Classification
Each parsed document is classified into one of three categories — **Invoice**, **Purchase Order**, or **Receipt** — using `ai_classify()`. Results are stored in `workspace.idp.classified_files`.

### 3. Structured Data Extraction
`ai_extract()` pulls specific fields from each document type:

| Document Type   | Fields Extracted                                                      |
|-----------------|-----------------------------------------------------------------------|
| Invoice         | Invoice No, Invoice Date, Terms, Currency, Payment Method, Total      |
| Purchase Order  | Buyer Name, PO Number, Total                                          |
| Receipt         | Receipt No, Total                                                     |

Each type is stored in its own Delta table: `invoices`, `purchase_orders`, `receipts`.

### 4. Automated Trigger
A Databricks Job was configured to trigger the notebook automatically when a new file arrives in the volume, enabling a fully automated, event-driven pipeline.

## Repository Structure

```
├── Notebooks/
│   └── final_project.ipynb   # Main pipeline notebook
└── Raw pdf files/
    ├── New Data/             # Additional sample PDFs
    └── *.pdf                 # Sample invoices, purchase orders, receipts
```

## Technologies Used

- **Databricks** — Notebook execution, Delta tables, Jobs, Volumes
- **Databricks AI Functions** — `ai_parse_document`, `ai_classify`, `ai_extract`
- **Delta Lake** — Structured output storage
- **SQL** — All pipeline steps written in Databricks SQL
