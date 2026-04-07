# Business Scope

## Project Domain
Retail banking payments and risk

## Business Problem
Data teams lose time diagnosing failed quality checks, locating governance policies, and understanding downstream impact across payments and risk datasets.

## Assistant Goals
The assistant should help users:
1. Investigate data quality failures
2. Answer governance and policy questions
3. Explain lineage and downstream impact

## Core Business Datasets
1. transactions
2. customers
3. accounts
4. risk_events
5. merchants

## Relationship Summary
- One customer can own multiple accounts
- One account can generate multiple transactions
- One transaction is linked to one merchant
- One transaction can generate zero or more risk events

## Target Users
- Data engineers
- Risk analysts
- Data stewards
- Governance stakeholders

## Expected Outcomes
- Faster data quality triage
- Better policy discoverability
- Clearer downstream impact analysis