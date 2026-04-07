# Bronze Data Quality Rules

## Purpose
These rules define the minimum data quality expectations for raw payments and risk data in the Bronze layer. They are used to identify issues early before data is standardized and enriched in the Silver layer.

---

## 1. bronze_transactions_raw

| Rule ID | Rule Name | Check Type | Rule Description | Severity | Why It Matters |
|---|---|---|---|---|---|
| TXN_001 | transaction_id_not_null | Completeness | transaction_id must not be null | Critical | Transactions cannot be uniquely identified without an ID |
| TXN_002 | transaction_id_unique | Uniqueness | transaction_id should be unique within the raw transaction dataset | Critical | Duplicate transaction IDs can distort reporting and risk calculations |
| TXN_003 | customer_id_not_null | Completeness | customer_id must not be null | High | Transactions must link to a customer for downstream analysis |
| TXN_004 | account_id_not_null | Completeness | account_id must not be null | High | Transactions should link to an account |
| TXN_005 | merchant_id_not_null | Completeness | merchant_id must not be null | Medium | Merchant enrichment and risk analysis depend on merchant mapping |
| TXN_006 | transaction_amount_positive | Validity | transaction_amount must be greater than 0 | Critical | Zero or negative amounts are invalid for this transaction type |
| TXN_007 | transaction_currency_allowed | Validity | transaction_currency must be one of AUD, USD, GBP, EUR, SGD | High | Invalid currency codes break downstream reporting and controls |
| TXN_008 | transaction_status_allowed | Validity | transaction_status must be one of settled, pending, failed, reversed | Medium | Status values must be standardized for reporting |
| TXN_009 | payment_channel_allowed | Validity | payment_channel must be one of card, transfer, online, direct_debit, atm | Medium | Standardized channel values are needed for analytics |
| TXN_010 | transaction_timestamp_not_null | Completeness | transaction_timestamp must not be null | High | Missing timestamps prevent trend analysis and sequencing |
| TXN_011 | transaction_country_allowed | Validity | transaction_country must be a valid ISO-style country code from the approved list | Medium | Country is important for geographic risk logic |
| TXN_012 | merchant_category_not_null_threshold | Completeness Threshold | merchant_category null rate should remain below 5% | Medium | Missing categories weaken merchant and risk analysis |

---

## 2. bronze_customers_raw

| Rule ID | Rule Name | Check Type | Rule Description | Severity | Why It Matters |
|---|---|---|---|---|---|
| CUST_001 | customer_id_not_null | Completeness | customer_id must not be null | Critical | Customer records require a stable identifier |
| CUST_002 | customer_id_unique | Uniqueness | customer_id should be unique | Critical | Duplicate customer IDs create ambiguous joins |
| CUST_003 | full_name_not_null | Completeness | full_name must not be null | High | Name is a key identity attribute in source records |
| CUST_004 | date_of_birth_not_null | Completeness | date_of_birth must not be null | High | Missing DOB weakens customer validation and governance controls |
| CUST_005 | date_of_birth_not_future | Validity | date_of_birth must not be in the future | Critical | Future DOB values are clearly invalid |
| CUST_006 | customer_segment_allowed | Validity | customer_segment must be one of retail, business, premium | Medium | Segment values must be standardized |
| CUST_007 | risk_rating_allowed | Validity | risk_rating must be one of low, medium, high | High | Risk reporting depends on valid risk classifications |
| CUST_008 | pep_flag_allowed | Validity | pep_flag must be one of Y or N | High | PEP flag is compliance-sensitive and must be consistent |
| CUST_009 | customer_status_allowed | Validity | customer_status must be one of active, inactive, blocked | Medium | Status consistency supports reporting and controls |

---

## 3. bronze_accounts_raw

| Rule ID | Rule Name | Check Type | Rule Description | Severity | Why It Matters |
|---|---|---|---|---|---|
| ACC_001 | account_id_not_null | Completeness | account_id must not be null | Critical | Accounts require a unique identifier |
| ACC_002 | account_id_unique | Uniqueness | account_id should be unique | Critical | Duplicate account IDs create join ambiguity |
| ACC_003 | customer_id_not_null | Completeness | customer_id must not be null | High | Every account should map to a customer |
| ACC_004 | account_type_allowed | Validity | account_type must be one of savings, transaction, credit | Medium | Supports consistent classification |
| ACC_005 | account_status_allowed | Validity | account_status must be one of active, dormant, closed | Medium | Status must be standardized |
| ACC_006 | account_currency_allowed | Validity | account_currency must be one of AUD, USD, GBP, EUR, SGD | Medium | Invalid currencies break reporting logic |
| ACC_007 | balance_reasonable_range | Reasonableness | balance should be between -100000 and 10000000 | Medium | Extreme balances may indicate source or parsing errors |
| ACC_008 | account_open_date_not_future | Validity | account_open_date must not be in the future | High | Future account open dates are invalid |

---

## 4. bronze_risk_events_raw

| Rule ID | Rule Name | Check Type | Rule Description | Severity | Why It Matters |
|---|---|---|---|---|---|
| RISK_001 | risk_event_id_not_null | Completeness | risk_event_id must not be null | Critical | Risk events require a stable identifier |
| RISK_002 | risk_event_id_unique | Uniqueness | risk_event_id should be unique | Critical | Duplicate risk events distort alert counts |
| RISK_003 | transaction_id_not_null | Completeness | transaction_id must not be null | High | Risk events should link to a triggering transaction |
| RISK_004 | customer_id_not_null | Completeness | customer_id must not be null | High | Customer-level risk analysis requires linkage |
| RISK_005 | risk_rule_id_not_null | Completeness | risk_rule_id must not be null | High | Rule attribution is needed for diagnostics |
| RISK_006 | risk_score_range | Validity | risk_score should be between 0 and 100 | Critical | Scores outside range are invalid |
| RISK_007 | risk_level_allowed | Validity | risk_level must be one of low, medium, high, critical | High | Severity must be standardized |
| RISK_008 | alert_flag_allowed | Validity | alert_flag must be one of Y or N | Medium | Alert logic depends on consistent flag values |
| RISK_009 | event_timestamp_not_null | Completeness | event_timestamp must not be null | High | Missing event timestamps limit auditability |

---

## 5. bronze_merchants_raw

| Rule ID | Rule Name | Check Type | Rule Description | Severity | Why It Matters |
|---|---|---|---|---|---|
| MERCH_001 | merchant_id_not_null | Completeness | merchant_id must not be null | Critical | Merchants need a stable identifier |
| MERCH_002 | merchant_id_unique | Uniqueness | merchant_id should be unique | Critical | Duplicate merchant IDs create enrichment ambiguity |
| MERCH_003 | merchant_name_not_null | Completeness | merchant_name must not be null | Medium | Missing names reduce interpretability |
| MERCH_004 | merchant_category_not_null | Completeness | merchant_category must not be null | Medium | Category is needed for segmentation and risk logic |
| MERCH_005 | merchant_country_allowed | Validity | merchant_country must be a valid ISO-style country code from the approved list | Medium | Geographic classification must be valid |
| MERCH_006 | merchant_risk_rating_allowed | Validity | merchant_risk_rating must be one of low, medium, high | High | Merchant risk classification must be standardized |
| MERCH_007 | sanctions_flag_allowed | Validity | sanctions_flag must be one of Y or N | Critical | Sanctions fields are compliance-sensitive and must be valid |

---

## Cross-Table Rules

| Rule ID | Rule Name | Check Type | Rule Description | Severity | Why It Matters |
|---|---|---|---|---|---|
| XREF_001 | accounts_customer_fk_valid | Referential Integrity | Every accounts.customer_id should exist in customers.customer_id | High | Broken account-to-customer mapping affects downstream joins |
| XREF_002 | transactions_customer_fk_valid | Referential Integrity | Every transactions.customer_id should exist in customers.customer_id | High | Transactions must map to valid customers |
| XREF_003 | transactions_account_fk_valid | Referential Integrity | Every transactions.account_id should exist in accounts.account_id | High | Transactions must map to valid accounts |
| XREF_004 | transactions_merchant_fk_valid | Referential Integrity | Every transactions.merchant_id should exist in merchants.merchant_id | Medium | Merchant enrichment requires valid merchant mapping |
| XREF_005 | risk_events_transaction_fk_valid | Referential Integrity | Every risk_events.transaction_id should exist in transactions.transaction_id | High | Risk events must map to source transactions |

---

## Design Notes

### Why these checks were chosen
These rules cover the main data quality dimensions needed for a realistic Bronze layer:
- completeness
- uniqueness
- validity
- referential integrity
- reasonableness

### Why severities differ
Severity reflects business impact:
- Critical = issue breaks trust, traceability, or core processing
- High = issue materially affects analytics, risk logic, or controls
- Medium = issue degrades quality but may not fully block processing

### Why threshold-based checks are useful
Not every issue should fail the table immediately. For example, a small number of missing merchant categories may be tolerable, but once the null rate crosses a threshold, the issue becomes operationally significant.

### Why referential integrity matters
Broken links between customers, accounts, transactions, merchants, and risk events can cause downstream data loss, inaccurate exposure reporting, and incomplete lineage.