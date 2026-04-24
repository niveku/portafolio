---
title: "Casaideas SAP-to-Cloud Pipeline"
summary: "Built key stages of an end-to-end data pipeline migrating daily SAP extracts into a cloud analytics layer for a major Latin American retail chain."
date: 2023-01-01
category: Data Engineering
stack: ["AWS S3", "AWS Glue", "PySpark", "SQL", "Python"]
featured: false
---

## Problem

Casaideas, a large Latin American home goods retailer, needed to migrate operational data from their SAP system into a cloud-based analytics layer. Daily SAP extracts needed to be ingested, cleaned, and made available in a consistent, traceable form for downstream reporting and business intelligence.

## My Role

Technical contributor responsible for key stages of the extraction-to-final-data flow. I implemented data processing steps in PySpark, worked on traceability mechanisms, and handled schema validation and consistency checks across the pipeline.

## What I Built

- Extraction layer: SAP daily files land in S3 and are catalogued.
- Processing: AWS Glue jobs with PySpark transform raw extracts — cleaning, type normalization, deduplication, and business-rule application.
- Traceability: lineage tracking so every downstream record can be traced back to its source extract and transformation step.
- Output: final tables in a queryable format for analytics consumption.

## Outcome

A reliable daily pipeline delivering clean, traceable inventory and operational data to the analytics layer. The traceability design was deliberate — retail data (especially inventory and stock) needs auditability when business users question numbers.
