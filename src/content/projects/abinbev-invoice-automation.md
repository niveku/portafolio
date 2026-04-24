---
title: "AB InBev Invoice PDF Automation"
summary: "Acting architect on a serverless pipeline that normalized invoice PDFs from 30+ supplier formats into structured data for downstream business workflows."
date: 2023-06-01
category: Data Engineering
stack: ["AWS Lambda", "AWS Step Functions", "AWS Textract", "AWS SQS", "AWS DynamoDB", "AWS S3", "AWS CloudWatch", "Python"]
featured: true
---

## Problem

AB InBev received invoices from 30+ suppliers in highly variable PDF formats — different layouts, numeric conventions, partially handwritten fields, and inconsistent structure. Downstream business systems needed clean, normalized data. There was no automated path from raw PDFs to structured records.

## My Role

I was the implementation lead and acting architect in practice. I took the initial requirement — "extract and normalize invoice data" — and drove it from requirements clarification through architecture decisions, PDF readability improvements, cost-aware design, and testing-oriented delivery.

## What I Built

A fully serverless pipeline on AWS:

- **Ingest:** PDFs land in S3, triggering a Lambda function.
- **Extraction:** AWS Textract handles OCR and structured field extraction.
- **Orchestration:** Step Functions coordinate the extraction, validation, and retry flow.
- **Queue:** SQS decouples ingestion from downstream processing for resilience.
- **Storage:** DynamoDB holds normalized records; S3 retains originals.
- **Observability:** CloudWatch alarms and logs for operational visibility.

I also ran a benchmark phase to quantify extraction accuracy across provider types before committing to the architecture, and designed preprocessing steps to improve PDF readability for challenging formats.

## Outcome

The pipeline handled invoices from roughly 30–40 supplier types. The architecture was cost-aware (Textract is priced per page — batch sizing and preprocessing reduced unnecessary spend). Ambiguous formats were flagged for human review rather than silently dropped.
