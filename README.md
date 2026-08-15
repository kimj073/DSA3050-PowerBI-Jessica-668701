# DSA3050 - Public Procurement (PPRA Kenya) Project

Dataset: Kenya: Public Procurement Regulatory Authority (PPRA)
Source: https://data.open-contracting.org/en/publication/147#access

## Project overview
This project uses the PPRA (Kenya) procurement dataset to analyse public procurement activity, award values, suppliers, and process performance. The goal is to produce a clear, reproducible analytical workflow and a Power BI report answering a set of governance and procurement questions.

## Reason for selecting this dataset
- Relevance: Public procurement represents a major portion of government spending; analysing it yields insights into public-sector market structure and potential inefficiencies or concentration of suppliers.
- Richness: The PPRA / Open Contracting dataset contains multi-year records with parties, award values, procurement methods, contract dates and statuses — enabling time series, supplier analysis and concentration metrics.
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
Each procurement record describes a procurement process (tender) run by a procuring entity in Kenya under PPRA or related systems. Records may include multiple related objects: tender, awards, contracts and parties.

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
   - Deduplicate tenders/awards and flatten nested award/party objects into relational tables: tenders, awards, parties, contracts.
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

## Power Query transformations (Power BI / Power Query Editor)
Below are the main Power Query transformations applied while preparing the dataset for analysis. Each entry lists the Problem observed in the raw data, the Transformation applied in Power Query, the Reason for the change, and the Result achieved. Screenshots showing the Power Query steps are included where relevant (files in screenshots/).

1) Remove unnecessary / sensitive columns
- Problem: Several columns provided no analytical value (e.g., description, address, language with only "English"), or contained sensitive contact information (email, telephone) which should not be used in analytics.
- Transformation: Used Home → Remove Columns (or right-click column → Remove) to drop Description, Address, Email, Telephone, Timestamp, Language and Status columns.
- Reason: Removing non-analytical and sensitive columns reduces dataset size, simplifies downstream joins and ensures privacy compliance.
- Result: Dataset contains only fields relevant for analysis (tender identifiers, dates, values, procuring entity, suppliers, categories).

![Remove unnecessary columns](screenshots/b_remove%20unnecessary%20columns.png)

2) Split date-time fields into Date and Time
- Problem: DateTime columns contained combined date and time values that made it harder to analyse by date period.
- Transformation: Used Transform → Split Column by Delimiter or DateTime → Date to extract Date and Time parts (created separate date fields like publication_date, award_date_date).
- Reason: Time-of-day is not required for most procurement trend analyses; splitting ensures correct type for date aggregations.
- Result: Clear Date fields for time-series grouping and duration calculations.

![Splitting columns](screenshots/b_splitting%20columns.png)

3) Rename date fields correctly
- Problem: The CSV used inconsistent field names; a field labelled as "start" contained later dates while "end" contained earlier dates (mismatch between label and content).
- Transformation: Inspect column values and use Transform → Rename to give correct names (e.g., rename "tender_start" and "tender_end" to match observed semantics).
- Reason: Correct field names prevent incorrect duration calculations and misinterpretation during analysis.
- Result: Date fields accurately describe the values they hold and feed correct duration and ordering logic.

![Renaming date fields correctly](screenshots/b_renaming%20date%20fields%20correctly.png)

4) Change identifier column type to Text
- Problem: Table ID was numeric and Power Query treated it as a number.
- Transformation: Changed the column type from Number to Text (Transform → Data Type → Text) for the ID column.
- Reason: IDs are unique identifiers (keys) and should not be used in numeric calculations; treating them as text avoids accidental aggregation or rounding.
- Result: ID column is a stable key for de-duplication and joins.

![Renaming data fields (example)](screenshots/b_renaming%20data%20fields.png)

5) Group procurement type to infer higher-level category for imputation
- Problem: Procurement type / method had many granular values and missing entries.
- Transformation: Grouped by procurement_type to inspect counts and determine the mode or a higher-level grouping that can be used for imputing missing values.
- Reason: Grouping identifies dominant categories (mode) and informs safe mode-imputation for missing categorical cells.
- Result: A grouping table used to impute missing procurement types with a defensible higher-level value.

6) Mode imputation for categorical missing values (replace values)
- Problem: Key categorical columns contained missing values which would hamper grouping and analysis.
- Transformation: Used Transform → Replace Values or Add Column → Conditional Column to replace nulls with the mode (most frequent category) determined earlier.
- Reason: Mode imputation preserves category-level distributions and avoids dropping many records.
- Result: Fewer missing category values; analyses such as counts and proportions are more stable.

![Before mode imputation](screenshots/b_before%20mode%20imputation.png)
![Handling categorical missing values (mode imputation)](screenshots/b_handling%20categorical%20missing%20values(mode%20imputation).png)

7) Create reference and duplicate queries for safe transformations
- Problem: Working directly on the primary query risked losing original data or making irreversible changes during experimentation.
- Transformation: Right-clicked each CSV query → Reference to create a staging query (reference), and Duplicate when a copy of data at a certain step was needed.
- Reason: Reference and Duplicate queries allow branching (one query for cleaning, one for modeling) and make it easy to compare raw vs transformed data.
- Result: A set of reference queries that preserve raw inputs and provide stable staging tables for modeling and relationships.

![Reference queries](screenshots/b_reference%20queries.png)

8) Handle errors in partiesId and suppliersId and identify duplicates
- Problem: Some rows produced errors in partiesId or suppliersId (malformed nested objects) and duplicates were possible across joined tables.
- Transformation: Used Transform → Detect Data Type Errors (or Table.TransformColumns with try/otherwise in M) to catch errors and replace them with null or a cleaned value; used Group By to find duplicate ID counts.
- Reason: Errors block downstream steps; handling them ensures the query completes and duplicates can be handled deterministically.
- Result: Cleaned ID columns with errors handled; duplicate records identified for de-duplication.

![Handling errors](screenshots/b_handling%20errors.png)
![Error when grouping to find duplicates](screenshots/b_error%20when%20grouping%20to%20find%20duplicates.png)

9) Create conditional column award_value (categorise award amounts)
- Problem: Award value amounts are continuous and need to be binned for categorical analysis.
- Transformation: Add Column → Conditional Column to create award_value_category based on value_amount ranges (e.g., 0-1M, 1M-50M, 50M-200M, 200M+), using the value_amount numeric column.
- Reason: Binning award values simplifies distributional analysis and aids visualisations (stacked bars, treemaps) showing how many awards fall into bands.
- Result: A categorical award_value field that supports grouped summaries and visual comparisons.

![Value award conditional column](screenshots/b_value%20award%20conditional%20column.png)

10) Column profiling across the entire dataset (not just top 1,000 rows)
- Problem: Power Query's default preview may show only the top N rows which can hide duplicates or distribution issues.
- Transformation: Used View → Column Profile and set the profiling options to sample the entire dataset (or used the advanced options to disable preview sampling where supported), then inspected distinct counts, number of errors, and null counts for columns of interest.
- Reason: Full-column profiling ensures decisions (like deduplication and imputation) are based on full data rather than a partial preview.
- Result: Accurate counts for unique values, duplicates, errors and nulls which guided cleaning and validation.

![Column profiling](screenshots/b_column%20profiling.png)

11) Disable load for staging queries and build model relationships
- Problem: Not all queries should be loaded to the Power BI data model (some are staging/transform queries only).
- Transformation: Right-click query → Disable Load for intermediate queries; keep final tables enabled. Then in Power BI model view, create relationships between tenders, awards, parties, suppliers using the cleaned keys.
- Reason: Reduces model size and improves performance. Keeps intermediate steps for reproducibility but not loaded into memory.
- Result: A compact model with only analytical tables loaded and properly defined relationships.

![Disabling load for modelling](screenshots/c_diabling%20load%20for%20modelling.png)
![Model view example](screenshots/c_model.png)

## Notes on reproducibility and screenshots
- The screenshots live in the screenshots/ directory and show the Power Query steps and diagnostics referenced above. If you want additional step-by-step images (for each Transform step's exact menu clicks), I can add more focused screenshots or short annotated images.

---

Author: Jessica Kimani
Course: DSA3050 - Business Intelligence & Data Visualization
Date: 2026-08
