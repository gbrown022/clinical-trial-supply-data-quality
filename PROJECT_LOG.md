# Project Log

## 2026-10-28: Project Initiation

### Objective
Develop a portfolio project demonstrating Excel-based data quality assessment, cleanup, and validation using a synthetic clinical trial supply dataset.

### Business Context
Clinical supply operations depend on accurate and consistent shipment data. Inconsistent formats, missing values, duplicate records, and implausible quantities can undermine reporting and operational decisions.

### Initial Plan
- Establish a local project and GitHub repository.
- Preserve the raw dataset.
- Profile the data and document potential quality issues.
- Prioritize issues based on operational impact.
- Apply documented cleanup rules.
- Validate the cleaned output.
- Summarize findings and decisions.

### Working Principles
- Preserve the original source data.
- Investigate ambiguous records before changing them.
- Document transformation rules and decisions.
- Validate results after cleanup.
- Keep the project focused and manageable.
- Use synthetic data only.

### Status
Project scope defined, local repository created, synthetic source workbook added, and initial documentation published.

### Next Step
Profile the source data and document data-quality findings before making changes.

## 2026-11-09: Initial Data Profiling

### Work Completed
- Quantified missing Delivery Dates and classified records by Shipment Status.
- Identified six text-formatted Kits Requested values.
- Profiled Shipment Status values using a PivotTable.
- Investigated the singleton Pending status (SHP-0064).
- Investigated the trailing-space Delivered status (SHP-0093).
- Confirmed SHP-0013 is the only repeated Shipment ID.
- Completed a formula-based shipment/delivery chronology check.

### Key Results
- 59 records have missing Delivery Dates.
- 7 of those records are marked Delivered or Delivery Confirmed.
- 6 of 101 Kits Requested values (5.9%) are stored as text.
- 7 distinct Shipment Status labels were identified.
- 1 repeated Shipment ID was identified: SHP-0013.

### Status

Initial profiling checks completed for selected fields. Findings and recommended actions documented in the data quality assessment. Additional profiling may be performed as needed during the cleaning and validation phases.

### Next Step
Define and document transformation rules before creating the cleaned dataset.