
# Data Cleaning Rules

## Purpose

This document defines the rules and decisions used to transform the original synthetic clinical trial supply dataset into a cleaned dataset.

The original source data will remain unchanged. All transformations will be applied to a separate working copy and documented here.

## 1. Mechanical Corrections

Corrections that can be made consistently without changing the underlying business meaning.

### CR-01: Convert Numeric Values Stored as Text

**Affected field:** `Kits Requested`

**Observation:** Six records contain numeric-looking values stored as text.

**Rule:** Convert values that contain valid numeric quantities into numeric values in the cleaned dataset.

**Scope:** SHP-0020, SHP-0070, SHP-0087, SHP-0008, SHP-0053, and SHP-0038.

**Rationale:** Numeric values stored as text can interfere with calculations, sorting, aggregation, and validation.

**Validation:** Confirm that all six values are stored as numbers and that their original numeric values are preserved.

### CR-02: Remove Unnecessary Leading and Trailing Whitespace

**Affected fields:** Text fields, including `Shipment Status`.

**Observation:** At least one Shipment Status value contains trailing whitespace.

**Rule:** Remove leading and trailing whitespace from text values where identified. Do not alter internal spacing or substantive wording.

**Rationale:** Unnecessary whitespace can cause inconsistent filtering, grouping, matching, and reporting.

**Validation:** Check affected text fields for remaining leading or trailing whitespace. Confirm that no substantive text has been changed.

## 2. Potential Data Errors

These records contain values or patterns that may indicate data-quality problems but cannot be corrected confidently without further investigation.

### CR-03: Investigate Negative Kits Requested Quantity

**Affected record:** SHP-0075

**Observation:** `Kits Requested` contains a negative quantity.

**Rule:** Do not convert the negative quantity to a positive value or replace it with zero without supporting evidence.

**Investigation:** Determine whether the value represents an adjustment, reversal, or data-entry error.

**Treatment:** Preserve the original value and flag the record until its meaning can be established.

### CR-04: Investigate Unusually High Kits Shipped Quantity

**Affected record:** SHP-0041

**Observation:** `Kits Shipped` contains a quantity of 999, which is unusually high relative to the other records.

**Rule:** Do not automatically cap, reduce, or replace the quantity.

**Investigation:** Verify the value against a trusted source and determine whether it represents a legitimate bulk shipment or an error.

**Treatment:** Preserve the original value and flag the record pending verification.

### CR-05: Investigate Potential Duplicate Shipment

**Affected record:** SHP-0013

**Observation:** The Shipment ID appears twice. The records match across all compared fields except Notes.

**Rule:** Do not automatically delete either record.

**Investigation:** Compare both records against the original source export to determine whether they represent the same shipment.

**Treatment:** Preserve both records until duplication is confirmed. If confirmed, document the decision to exclude one record from the cleaned output.

## 3. Business-Rule Questions

These issues require clarification of business definitions or verification against a trusted source before corrective action can be taken.

### CR-06: Investigate Missing Delivery Dates

**Affected field:** `Delivery Date`

**Observation:** 59 records have missing Delivery Dates. Seven are marked Delivered or Delivery Confirmed.

**Rule:** Do not infer or manufacture delivery dates.

**Investigation:** Determine whether delivery dates can be recovered from a trusted shipment or depot record. Prioritize the seven records marked Delivered or Delivery Confirmed.

**Treatment:** Preserve missing values until verified dates can be obtained. Flag completed-delivery records with missing dates as exceptions.

### CR-07: Investigate Missing Site IDs

**Affected field:** `Site ID`

**Observation:** Three records have missing Site IDs.

**Rule:** Do not infer Site IDs from neighboring records or other unrelated fields.

**Investigation:** Determine whether the correct Site IDs can be recovered from a trusted source.

**Treatment:** Preserve missing values and flag unresolved records.

### CR-08: Investigate Missing Kits Shipped Quantities

**Affected field:** `Kits Shipped`

**Affected records:** SHP-0033 and SHP-0067

**Observation:** Two records have missing Kits Shipped quantities.

**Rule:** Do not substitute requested quantities or other assumed values.

**Investigation:** Verify shipped quantities against a trusted shipment record.

**Treatment:** Preserve missing values until verified quantities are available.

### CR-09: Review Shipment Status Definitions and Consistency

**Affected field:** `Shipment Status`

**Observation:** Seven distinct stored labels were identified in the initial PivotTable analysis. Some additional capitalization and spacing variations may require separate examination.

**Rule:** Standardize obvious formatting variations only where the underlying status meaning is unchanged. Do not combine statuses solely because their wording appears similar.

**Investigation:** Confirm the intended definitions of `Delivered`, `Delivery Confirmed`, `In Transit`, `In-Transit`, `Pending`, and `Pending Dispatch`.

Pay particular attention to:

- SHP-0064, which has a `Pending` status despite having shipped and delivered quantities and a Delivery Date.
- SHP-0093, which has a trailing-space variation of `Delivered` and a missing Delivery Date.

**Treatment:** Preserve potentially meaningful status distinctions until their definitions are confirmed. Document any approved standardization.

## 4. Unresolved Records

Records that cannot be reliably corrected using the available information will remain unchanged in the cleaned dataset.

The following principles apply:

- Preserve original values when their intended meaning cannot be established.
- Do not invent missing values or infer corrections without supporting evidence.
- Flag unresolved records so they can be identified in reporting and downstream analysis.
- Document the reason for retaining each unresolved issue.
- Revisit unresolved records if additional source information becomes available.

## 5. Validation Requirements

The cleaned dataset will be checked against the following requirements:

1. All six identified `Kits Requested` text values are stored as numeric values, with their original quantities preserved.
2. Leading and trailing whitespace has been removed from applicable text fields.
3. No records have been deleted without documented justification.
4. Missing values have not been replaced with assumed or inferred values.
5. Shipment Status changes are consistent with documented business definitions.
6. Potential anomalies and unresolved records remain identifiable.
7. Shipment and Delivery Dates are chronologically consistent wherever both dates are present.
8. Record counts and key identifiers are reconciled against the original source dataset.
9. All transformations are documented and reproducible.
10. The cleaned dataset is compared against the original source to confirm that all changes are accounted for and no unintented changes have occurred.
