# Bronze Schema Design

## Purpose
The Bronze layer stores raw banking payments and risk data before validation, standardization, and enrichment in the Silver layer.

---

## 1. bronze_transactions_raw

| Column Name | Data Type | Description | Sensitive? | Notes |
|---|---|---|---|---|
| transaction_id | string | Unique transaction identifier | No | May contain duplicates in raw data |
| customer_id | string | Customer linked to the transaction | Yes | Joins to customer data |
| account_id | string | Account used for the transaction | Yes | Joins to account data |
| transaction_timestamp | timestamp | Date and time of transaction | No | Important for trend and anomaly analysis |
| merchant_id | string | Merchant identifier | No | Joins to merchant table |
| merchant_category | string | Merchant business category | No | May be missing or inconsistent |
| transaction_amount | decimal(18,2) | Transaction amount | No | Used in risk and exposure analysis |
| transaction_currency | string | Currency code | No | Expected values like AUD, USD, GBP |
| transaction_country | string | Country where transaction occurred | No | Used in risk logic |
| payment_channel | string | Payment method or channel | No | e.g. card, transfer, online |
| transaction_status | string | Status of transaction | No | e.g. settled, pending, failed |

---

## 2. bronze_customers_raw

| Column Name | Data Type | Description | Sensitive? | Notes |
|---|---|---|---|---|
| customer_id | string | Unique customer identifier | Yes | Primary join key |
| full_name | string | Customer full name | Yes | PII |
| date_of_birth | date | Customer date of birth | Yes | Sensitive personal data |
| customer_segment | string | Segment classification | No | e.g. retail, business, premium |
| residence_country | string | Country of residence | Yes | May be sensitive depending on policy |
| risk_rating | string | Customer risk classification | Yes | e.g. low, medium, high |
| pep_flag | string | Politically exposed person flag | Yes | Sensitive governance field |
| customer_status | string | Status of customer | No | e.g. active, inactive, blocked |

---

## 3. bronze_accounts_raw

| Column Name | Data Type | Description | Sensitive? | Notes |
|---|---|---|---|---|
| account_id | string | Unique account identifier | Yes | Primary join key |
| customer_id | string | Linked customer identifier | Yes | Foreign key to customer |
| account_type | string | Type of account | No | e.g. savings, transaction, credit |
| account_open_date | date | Account opening date | No | Used for account age logic |
| account_status | string | Status of account | No | e.g. active, dormant, closed |
| account_currency | string | Currency of account | No | Typically AUD in local banking scenario |
| balance | decimal(18,2) | Current account balance | Yes | Sensitive financial field |

---

## 4. bronze_risk_events_raw

| Column Name | Data Type | Description | Sensitive? | Notes |
|---|---|---|---|---|
| risk_event_id | string | Unique risk event identifier | No | Primary key for event records |
| transaction_id | string | Linked transaction identifier | No | Foreign key to transaction |
| customer_id | string | Linked customer identifier | Yes | Used in customer-level risk analysis |
| risk_rule_id | string | Identifier of triggered risk rule | No | Supports DQ and assistant explanations |
| risk_score | decimal(5,2) | Risk score assigned to event | No | Used for thresholds and alerts |
| risk_level | string | Risk severity level | No | e.g. low, medium, high, critical |
| alert_flag | string | Indicates whether alert was raised | No | Expected yes/no or true/false style values |
| alert_reason | string | Explanation for the alert | No | Useful for assistant responses |
| event_timestamp | timestamp | Date and time of risk event | No | Used for trend and audit analysis |

---

## 5. bronze_merchants_raw

| Column Name | Data Type | Description | Sensitive? | Notes |
|---|---|---|---|---|
| merchant_id | string | Unique merchant identifier | No | Primary join key |
| merchant_name | string | Merchant name | No | Used in reporting and explanations |
| merchant_category | string | Merchant category | No | Expected to align with transaction data |
| merchant_country | string | Merchant country | No | Useful in geographic risk analysis |
| merchant_risk_rating | string | Merchant risk classification | No | e.g. low, medium, high |
| sanctions_flag | string | Indicates sanctions-related concern | Yes | Sensitive governance / compliance field |

---

## Design Notes

### Why these tables exist
These five tables provide the minimum realistic structure needed to support:
- payment activity analysis
- customer and account relationships
- merchant enrichment
- risk event generation
- governance and lineage use cases

### Why some fields are marked sensitive
Fields are marked sensitive where they may contain:
- personally identifiable information
- financially sensitive information
- compliance-sensitive indicators
- restricted risk classifications

### Why Bronze data may be imperfect
Bronze is intentionally close to source form and may contain:
- nulls
- duplicate records
- invalid codes
- inconsistent formats
- delayed or missing relationships

These imperfections are useful because they create realistic problems for Silver cleaning and for assistant explanations.