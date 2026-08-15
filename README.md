# DSA3050 - Public Procurement (PPRA Kenya) Project

Dataset: Kenya: Public Procurement Regulatory Authority (PPRA)
Source: https://data.open-contracting.org/en/publication/147#access

## Project overview
This project uses the PPRA (Kenya) procurement dataset to analyse public procurement activity, award values, suppliers, and process performance. The goal is to produce a clear, reproducible analytics report and interactive Power BI dashboard that answers policy- and procurement-relevant questions about who is awarded contracts, how award values change over time, and where data quality issues exist.

## Reason for selecting this dataset
- Relevance: Public procurement represents a major portion of government spending; analysing it yields insights into public-sector market structure and potential inefficiencies or concentration of awards.
- Richness: The PPRA / Open Contracting dataset contains multi-year records with parties, award values, procurement methods, contract dates and statuses — enabling time series, supplier analysis and process-duration analytics.
- Accessibility and transparency: The data is open and documented, which supports reproducible analysis and aligns with public policy and auditing use-cases.

## Main data variables (typical fields to expect)
- ocid / tender_id / procurement_id — unique tender identifier
- title / description — tender title / description
- procuring_entity / buyer_name — name of the government entity issuing the tender
- procurement_category / item classifications / cpv — category or classification of goods/services
- procurement_method — e.g., Open, Restricted, Direct/Sole Source, Competitive Negotiation
- award_id — unique award identifier
- award_date — award publication or award decision date
- award_value / value.amount / value.currency — monetary value of award
- supplier / supplier_name / parties — winning supplier(s) and their details
- contract_id — linked contract identifier
- contract_start / contract_end — contract period
- status / tender_status / award_status — current completion status
- bid_count / number_of_bids — how many bids received (if available)
- lots / lot_id — lot information (if tenders are split)

Note: field names may vary in the downloaded JSON/CSV. We'll map the dataset's schema to these canonical names during ETL.

## What the data represents
Each procurement record describes a procurement process (tender) run by a procuring entity in Kenya under PPRA or related systems. Records may include multiple related objects: tender, awards, contracts, and parties. Award and contract objects capture the outcome (who won, award value, contract terms). Together they represent the flow from procurement announcement to award and contract execution.

## Business / analytical questions (from your brief)
1. How have the number and value of Kenyan public procurement processes changed over time?
2. Which procuring entities and procurement categories account for the highest total award values?
3. Which suppliers receive the largest number and value of public procurement awards?
4. How concentrated are procurement awards among the largest suppliers?
5. Which procurement methods are most frequently used, and how do their award values compare?
6. Which entities have the longest procurement-processing durations?
7. What proportion of procurement records contain missing values, missing parties or missing award and contract information?

## Which questions I intend to investigate (project scope)
I plan to investigate all seven questions above, prioritised as follows:
- Core time-series and volume/value trends (Q1)
- Top procuring entities and categories by award value (Q2)
- Supplier-level analysis: counts, total values and concentration (Q3 & Q4)
- Procurement method frequency and value comparison (Q5)
- Procurement-process durations by entity and category (Q6)
- Data quality assessment and missingness profiling to support interpretation (Q7)

These questions will produce both descriptive insights and diagnostic metrics useful for procurement oversight.

## Proposed methodology
1. Data ingestion & schema mapping
   - Download the PPRA / Open Contracting data (JSON or CSV) and map fields to the canonical schema.
2. Data cleaning
   - Normalize currencies (if multiple currencies appear) or convert to a single base currency if feasible.
   - Parse and standardise dates (award_date, contract_start/end, publication_date).
   - Deduplicate tenders/awards and flatted nested award/party objects into relational tables: tenders, awards, parties, contracts.
3. ETL and feature engineering
   - Compute derived fields: award_value_numeric, award_year, duration_days (award to contract signature or publication to award), supplier_id, procuring_entity_id.
4. Analysis and visualisations
   - Q1: Time series plots (count of tenders per period; total award value per period; CAGR or year-over-year % change).
   - Q2: Bar charts / treemap of top procuring entities and top categories by award value and counts.
   - Q3: Tables and charts of top suppliers by number and value of awards.
   - Q4: Concentration metrics (top-N share, Herfindahl–Hirschman Index, Lorenz curve and Gini coefficient for award value distribution).
   - Q5: Frequency and award-value distribution by procurement method (histogram, boxplots, stacked bars).
   - Q6: Processing durations: compute durations and show entities with longest median/mean durations and distribution plots.
   - Q7: Data quality dashboard: percent missing for key fields, missing parties, missing award/contract links and guideline notes.
5. Tools
   - Power BI for dashboarding and interactive visuals (primary deliverable).
   - Python (pandas) or SQL for data cleaning, aggregation and metrics.
   - Jupyter/Markdown or notebook for reproducible steps and code snippets.

## Deliverables
- Cleaned dataset files (CSV/Parquet) and ETL notes
- Power BI report with interactive dashboards answering the research questions
- A short analytical report (PDF/Markdown) summarising methods, findings and recommendations
- README (this file) documenting approach and replication steps

## Next steps (short-term plan)
1. Obtain and inspect the PPRA dataset from the given link.
2. Map actual field names and sample rows; create a small exploratory notebook to confirm types and missingness.
3. Implement ETL scripts to flatten awards/parties into tables.
4. Begin Power BI report with core charts: time series and top entities/suppliers.

## References & data access
- Open Contracting Data Standard (OC4IDS) / PPRA publication: https://data.open-contracting.org/en/publication/147#access

---

Author: Jessica Kimani
Course: DSA3050 - Business Intelligence & Data Visualization
Date: 2026-08
