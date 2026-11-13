# Data Quality Assessment

## 1. Purpose

This assessment documents the initial data-quality review of a synthetic clinical trial supply shipment dataset. The objective is to identify completeness issues, inconsistencies, potential anomalies, and records requiring further investigation before data cleaning begins.

The assessment distinguishes observed conditions from confirmed errors. Records are not automatically corrected or removed solely because they appear unusual.

## 2. Scope and Method

The initial review covered the shipment dataset using visual inspection, Excel filtering and sorting, duplicate-value highlighting, formula-based validation, and exploratory Power Query profiling of text fields and column completeness.

The following areas were examined:

- Missing values in key shipment fields
- Consistency of shipment status labels
- Numeric values and potentially anomalous quantities
- Potential duplicate shipment records
- Shipment and delivery date chronology
- Additional profiling of text fields and missing key identifiers

The findings are preliminary. Some checks were performed systematically across the dataset, while others were based on initial visual inspection. All findings should be validated before being treated as final.

## 3. Summary of Findings

| ID | Field | Observation | Classification | Status |
|---|---|---|---|---|
| DQ-01 | Site ID | Three records have missing Site IDs. | Completeness | Observed |
| DQ-02 | Delivery Date / Shipment Status | Seven records marked Delivered or Delivery Confirmed have missing Delivery Dates. | Completeness / cross-field consistency | Observed |
| DQ-03 | Notes | Some records have blank Notes fields. | Completeness | Observed; significance undetermined |
| DQ-04 | Kits Requested | Six numeric-looking values were stored as text. | Data type consistency | Confirmed; corrected in cleaned dataset and validated |
| DQ-05 | Shipment Status | Variations include Delivered, Delivery Confirmed, and DELIVERY CONFIRMED. | Standardization / terminology | Observed; semantic equivalence requires confirmation |
| DQ-06 | Shipment Status | Variations include In Transit, In transit, and In-Transit. | Standardization | Observed |
| DQ-07 | Kits Shipped | SHP-0033 and SHP-0067 have missing quantities. | Completeness | Observed |
| DQ-08 | Kits Requested | SHP-0075 has a negative quantity. | Potential validity issue | Requires investigation |
| DQ-09 | Kits Shipped | SHP-0041 has a quantity of 999. | Potential outlier | Requires investigation |
| DQ-10 | Shipment ID | SHP-0013 appears in rows 14 and 15. All compared fields match except Notes; the second record notes "Duplicate? added from weekly export". | Potential duplicate | Strong duplicate candidate; confirmation required |
| DQ-11 | Shipment / Delivery Date | Formula-based comparison flagged missing dates but did not flag delivery dates earlier than shipment dates. | Chronological consistency | No violations detected by this check |
| DQ-12 | Shipment ID | Two records for study STDY-C04 have missing Shipment IDs: Germany (DE), Site 401; and Spain (ES), Site 501. | Completeness / key identifier | Newly identified; source verification required |

## 4. Detailed Findings and Recommended Actions

### DQ-01: Missing Site IDs

**Observation:** Three records have blank Site IDs.

**Potential impact:** Missing identifiers may prevent reliable association of shipments with clinical sites and affect site-level reporting or reconciliation.

**Recommended action:** Investigate whether the missing identifiers can be recovered from an authoritative source.

**Cleaning decision:** Do not infer or populate Site IDs without verification.

### DQ-02: Missing Delivery Dates

**Observation:** 59 records have missing Delivery Dates. Their Shipment Status breakdown is:

| Shipment Status | Missing Delivery Dates |
|---|---:|
| Delivered / Delivery Confirmed | 7 |
| In Transit | 27 |
| Pending / Pending Dispatch | 25 |
| **Total** | **59** |

The seven completed-delivery records are:

- SHP-0010
- SHP-0029
- SHP-0078
- SHP-0093
- SHP-0022
- SHP-0062
- SHP-0090

**Potential impact:** Missing delivery dates may affect shipment history, delivery performance reporting, and reconciliation.

**Recommended action:** Investigate whether delivery dates can be recovered from a trusted source.

**Cleaning decision:** Do not infer delivery dates from shipment dates or other unrelated records.

### DQ-03: Blank Notes

**Observation:** Some records have blank Notes fields.

**Potential impact:** Blank Notes may be harmless if the field is optional. They may matter if Notes are required to document exceptions or special handling.

**Recommended action:** Confirm whether Notes is optional or required under the intended business process.

**Cleaning decision:** Do not treat blank Notes as a defect until the field's requirements are established.

### DQ-04: Numeric Values Stored as Text

**Observation:** Six of 101 records (5.9%) have numeric-looking values in `Kits Requested` stored as text.

| Shipment ID | Kits Requested |
|---|---:|
| SHP-0020 | 24 |
| SHP-0070 | 24 |
| SHP-0087 | 24 |
| SHP-0008 | 36 |
| SHP-0053 | 12 |
| SHP-0038 | 60 |

**Potential impact:** Text-formatted quantities can interfere with calculations, sorting, aggregation, and numeric validation.

**Recommended action:** Convert valid numeric text values to numbers in the cleaned dataset and verify that all affected values are correctly interpreted.

**Cleaning decision:** Preserve the original source data. Document the conversion and validate the resulting numeric values in the cleaned dataset.

**Cleaning status:** The six identified values were converted in the separate cleaned workbook. A formula-based check returned zero text values remaining in `Kits Requested`; the original quantities were confirmed as preserved. The raw source workbook remains unchanged.

### DQ-05 and DQ-06: Shipment Status Inconsistencies

**Observation:** The initial PivotTable analysis identified seven distinct stored labels across 101 records. Variations include differences in terminology, capitalization, punctuation, and spacing. A later exploratory Power Query profile displayed eight distinct values for `Shipment Status`. The difference between the two profiling results has not yet been reconciled; capitalization or other text variations may account for it.

| Shipment Status | Record Count |
|---|---:|
| Delivered | 21 |
| Delivered (trailing space) | 1 |
| Delivery Confirmed | 25 |
| In Transit | 25 |
| In-Transit | 2 |
| Pending | 1 |
| Pending Dispatch | 26 |
| **Total** | **101** |

**Additional observations:**

- SHP-0064 is the only record with status `Pending`. It has 24 Kits Requested and 24 Kits Shipped, a Delivery Date three days after its Ship Date, and Notes stating "Routine resupply". This may indicate a status inconsistency, but the intended business definition requires confirmation.
- SHP-0093 had a trailing space in its `Delivered` status and also has a missing Delivery Date. The trailing space was removed manually in the cleaned workbook; the raw source remains unchanged. The missing Delivery Date remains unresolved.

**Potential impact:** Inconsistent status values may cause equivalent shipment states to be counted separately in filters, summaries, and reports. Incorrectly combining distinct operational states could also remove meaningful information.

**Recommended action:** Confirm the intended definitions of each status. Standardize capitalization, spacing, punctuation, and terminology where equivalence is established.

**Cleaning decision:** Treat obvious formatting variations separately from potential differences in business meaning. Do not automatically combine `Pending` and `Pending Dispatch`, or `Delivered` and `Delivery Confirmed`, without confirmation of their intended meanings.

### DQ-07: Missing Kits Shipped Values

**Observation:** SHP-0033 and SHP-0067 have blank Kits Shipped values.

**Potential impact:** Missing quantities may affect shipment-volume reporting, inventory reconciliation, and comparisons between requested and shipped quantities.

**Recommended action:** Investigate the source records to establish the actual quantities.

**Cleaning decision:** Do not substitute zero for an unknown quantity.

### DQ-08: Negative Kits Requested Quantity

**Observation:** SHP-0075 has a negative Kits Requested quantity.

**Potential impact:** An unexplained negative quantity may distort demand reporting, forecasting, or reconciliation.

**Recommended action:** Compare the record against its source and determine whether negative quantities are permitted for adjustments, reversals, or other defined processes.

**Cleaning decision:** Retain the original value until its meaning is established.

### DQ-09: Potentially Anomalous Kits Shipped Quantity

**Observation:** SHP-0041 has a Kits Shipped quantity of 999.

**Potential impact:** If erroneous, the value could distort shipment-volume summaries and reconciliation.

**Recommended action:** Verify the quantity against the source record and investigate whether it represents a legitimate bulk shipment.

**Cleaning decision:** Do not cap, replace, or remove the value solely because it is unusually large.

### DQ-10: Potential Duplicate Shipment Record

**Observation:** Shipment ID SHP-0013 appears twice, in rows 14 and 15. A field-by-field comparison found that all fields match except Notes. The second record includes the note "Duplicate? added from weekly export".

A PivotTable analysis of Shipment ID confirmed that SHP-0013 is the only repeated Shipment ID in the dataset, with two total occurrences.

**Potential impact:** If both records represent the same shipment, counting both could inflate shipment volumes and distort operational reporting or reconciliation.

**Recommended action:** Compare the records against the source export and determine whether the repeated entry is a duplicate or represents a legitimate business event.

**Cleaning decision:** Preserve both records in the source data. Exclude one from the cleaned output only after duplication is confirmed and the decision is documented.

### DQ-11: Shipment and Delivery Date Chronology

**Validation performed:** A formula-based check compared Ship Date (column J) and Delivery Date (column K) across all 101 records.

**Observation:** No records were flagged with a Delivery Date earlier than the corresponding Ship Date. The check identified missing dates, which are documented separately under DQ-02.

**Potential impact:** Chronologically invalid dates could undermine shipment history, delivery performance reporting, and operational analysis.

**Recommended action:** Retain the chronology check as part of the data-validation process. Investigate missing dates separately and verify date accuracy against trusted source records.

**Validation result:** No shipment/delivery chronology violations were detected by this check.

**Limitation:** This check establishes chronological consistency only. It does not establish that dates are accurate or operationally plausible.


### DQ-12: Missing Shipment IDs

**Observation:** Exploratory profiling of the cleaned-data copy identified two records with missing `Shipment ID` values. Both records belong to study `STDY-C04` and are associated with different sites:

| Study ID | Country | Site ID | Shipment ID |
|---|---|---:|---|
| STDY-C04 | Germany (DE) | 401 | Missing |
| STDY-C04 | Spain (ES) | 501 | Missing |

The records are associated with different sites. Their Site Names are not reproduced here because the exact names have not yet been captured in the assessment.

**Potential impact:** Missing Shipment IDs may prevent reliable shipment identification, source reconciliation, and record traceability.

**Recommended action:** Recover the correct Shipment IDs from a trusted source.

**Cleaning decision:** Do not generate or infer Shipment IDs. Preserve both records and flag the missing identifiers until verified values are available.

## 5. Prioritization

The following order is a preliminary recommendation based on potential reporting and operational impact. It is not a formal risk assessment.

1. **Resolve missing key information:** Investigate missing Site IDs, Shipment IDs, Kits Shipped quantities, and Delivery Dates for completed deliveries.
2. **Investigate potentially misleading records:** Review the negative requested quantity, unusually large shipped quantity, and potential duplicate shipment.
3. **Address calculation and reporting consistency:** Assess text-formatted numeric quantities and standardize shipment statuses where their meanings are confirmed.
4. **Clarify field requirements:** Determine whether blank Notes fields are acceptable.

## 6. Limitations

- The dataset is synthetic and intended for practice and demonstration.
- Findings are based on an initial review and subsequent exploratory profiling; they are not a complete audit of all fields or business rules.
- An exploratory formula scan returned 12 cells for which `LEN(value)` differed from `LEN(TRIM(value))`. This comparison can flag leading/trailing spaces and repeated internal spaces; the affected cells have not yet been isolated, so the count is not treated as 12 confirmed leading/trailing-whitespace defects.
- Power Query was used to inspect text fields and column quality. The exploratory query was discarded without loading its transformations to the worksheet; no bulk Power Query cleanup was applied.
- Several observations require source-data verification or confirmation of business definitions.
- No records have been declared erroneous solely because they appear unusual.
- The assessment does not establish that the dataset reflects the full complexity of a production clinical supply system.

## 7. Next Steps

1. Complete and reconcile the assessment, including the newly identified missing Shipment IDs and unresolved text-profiling results.
2. Maintain documented cleaning rules for each proposed transformation.
3. Continue cleaning in the separate cleaned workbook while preserving the raw source.
4. Apply only justified corrections and standardizations.
5. Validate the cleaned dataset against documented checks and reconcile it to the raw source.
6. Summarize the changes, unresolved exceptions, and validation results.

## 8. Working Principles

- Preserve the original source data.
- Investigate ambiguous records before changing them.
- Distinguish observed anomalies from confirmed errors.
- Document decisions and transformation rules.
- Validate results after cleaning.
- Use synthetic data only.