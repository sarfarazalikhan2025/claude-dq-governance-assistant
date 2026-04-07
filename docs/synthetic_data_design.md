# Synthetic Data Design

## Purpose
This document defines how synthetic banking payments and risk data will be generated for the Bronze layer, including both valid records and intentional bad data patterns to support data quality, governance, lineage, and assistant use cases.

---

## 1. Target Table Volumes

| Table Name | Target Volume | Purpose |
|---|---|---|
| bronze_customers_raw | 1,000 rows | Provides customer reference data with risk attributes |
| bronze_accounts_raw | 1,500 rows | Provides account relationships linked to customers |
| bronze_merchants_raw | 300 rows | Provides merchant reference and risk context |
| bronze_transactions_raw | 10,000 rows | Main operational table for payments activity |
| bronze_risk_events_raw | 2,000 rows | Risk events linked to higher-risk or flagged transactions |

---

## 2. General Design Principles

- Most records should be valid and usable
- A controlled percentage of records should contain data quality issues
- Relationships between tables should mostly work, with some intentional broken mappings
- Values should reflect realistic banking and risk distributions
- Sensitive fields should be included where needed for governance use cases
- Some issues should be row-level, while others should be threshold-based

---

## 3. Table-Level Design Patterns

### bronze_customers_raw

#### Normal patterns
- customer_id should follow a format such as CUST000001
- customer_segment should mostly be retail, with smaller numbers of business and premium customers
- risk_rating should mostly be low or medium, with fewer high-risk customers
- pep_flag should rarely be Y
- customer_status should mostly be active

#### Intentional bad patterns
- small number of duplicate customer_id values
- small number of null full_name values
- small number of null date_of_birth values
- very small number of future date_of_birth values
- occasional invalid risk_rating values such as extreme or unknown
- occasional invalid pep_flag values such as blank or X

---

### bronze_accounts_raw

#### Normal patterns
- account_id should follow a format such as ACC000001
- each customer may own one or more accounts
- account_type should mostly be savings or transaction, with some credit accounts
- account_status should mostly be active
- account_currency should mostly be AUD, with smaller numbers of USD, GBP, EUR, and SGD
- balances should mostly sit within a reasonable retail banking range

#### Intentional bad patterns
- small number of duplicate account_id values
- small number of null customer_id values
- small number of account_open_date values in the future
- occasional invalid account_type values such as loanish or unknown
- occasional invalid currency values such as AAA or blank
- very small number of extreme balances beyond the allowed range
- small number of accounts referencing a non-existent customer_id

---

### bronze_merchants_raw

#### Normal patterns
- merchant_id should follow a format such as MERCH0001
- merchant categories should include grocery, travel, electronics, fuel, hospitality, utilities, and ecommerce
- merchant_country should mostly be AU, with some international merchants
- merchant_risk_rating should mostly be low or medium
- sanctions_flag should almost always be N

#### Intentional bad patterns
- small number of duplicate merchant_id values
- small number of null merchant_name values
- small number of null merchant_category values
- occasional invalid merchant_country values such as XX or blank
- occasional invalid merchant_risk_rating values such as severe
- very small number of invalid sanctions_flag values such as maybe

---

### bronze_transactions_raw

#### Normal patterns
- transaction_id should follow a format such as TXN00000001
- transactions should map to valid customers, accounts, and merchants
- transaction_amount should mostly be between 5 and 5000
- a smaller number of transactions should be high-value outliers
- transaction_currency should mostly align with account currency
- transaction_country should mostly be AU, with some international values
- payment_channel should include card, transfer, online, direct_debit, and atm
- transaction_status should mostly be settled, with some pending, failed, and reversed values
- merchant_category should usually align with the merchant table

#### Intentional bad patterns
- small number of duplicate transaction_id values
- small number of null transaction_id values
- small number of null customer_id or account_id values
- occasional negative or zero transaction_amount values
- occasional invalid currency values such as ZZZ
- occasional invalid payment_channel values such as crypto_terminal
- occasional invalid transaction_status values such as processing_now
- occasional missing transaction_timestamp values
- small number of transactions referencing a non-existent merchant_id
- merchant_category null rate designed to be close to the rule threshold

---

### bronze_risk_events_raw

#### Normal patterns
- risk_event_id should follow a format such as RISK000001
- most risk events should link to valid transactions and customers
- risk_score should range between 0 and 100
- risk_level should broadly align with risk_score bands
- alert_flag should usually be Y for higher-risk events and N for lower-risk events
- alert_reason should reference plausible business scenarios such as high_value_transfer, unusual_country, sanctions_match, or repeated_decline_pattern

#### Intentional bad patterns
- small number of duplicate risk_event_id values
- small number of null transaction_id values
- small number of null risk_rule_id values
- occasional risk_score values below 0 or above 100
- occasional invalid risk_level values such as urgent
- occasional invalid alert_flag values such as maybe
- occasional missing event_timestamp values
- small number of risk events referencing a non-existent transaction_id

---

## 4. Suggested Bad Data Rates

| Issue Type | Suggested Rate |
|---|---|
| Nulls in required fields | 1% to 2% |
| Duplicate keys | 0.5% to 1% |
| Invalid categorical values | 1% |
| Referential integrity breaks | 1% |
| Extreme reasonableness issues | less than 0.5% |
| Threshold-based issue (e.g. merchant_category nulls) | approximately 4% to 6% |

---

## 5. Why the Bad Data Should Be Controlled

The data should not be too clean, because then the assistant has nothing meaningful to explain.

The data should also not be too broken, because then it stops looking realistic.

Controlled bad data allows the project to demonstrate:
- failed checks
- root cause analysis
- governance-sensitive fields
- threshold breaches
- impact analysis across related tables

---

## 6. Example Business Scenarios Enabled by This Design

- Duplicate transaction IDs inflate payment counts
- Missing merchant categories reduce risk segmentation quality
- Invalid currencies break reporting consistency
- Broken customer mappings affect account and transaction enrichment
- Out-of-range risk scores make risk logic unreliable
- Invalid sanctions flags create compliance risk
- Missing timestamps reduce auditability and trend analysis

---

## 7. Design Outcome

This synthetic dataset should look realistic enough to support:
- Bronze-to-Silver cleaning logic
- data quality incident reporting
- governance and classification questions
- lineage and downstream impact examples
- Claude-powered assistant explanations