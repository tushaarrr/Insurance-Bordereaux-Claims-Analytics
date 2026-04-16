# Insurance Bordereaux & Claims Analytics Dashboard

## Overview

This project transforms raw insurance policy and claims data into a recruiter-ready analytics solution built for P&C Insurance / Actuarial Data Analyst roles. The workbook includes data cleaning, KPI reporting, lookup formulas, business metrics, and an executive dashboard.

## Tools Used

* Microsoft Excel
* Advanced Formulas (INDEX/MATCH, VLOOKUP/XLOOKUP, SUMIFS, COUNTIFS, IFERROR)
* Charts and Dashboarding
* SQL (analysis queries below)

## Project Structure

* Raw_Data: Original imported CSV data
* Cleaned_Data: Standardized dataset with derived business columns
* KPI_Data: Metrics used for dashboard reporting
* Lookup_Analysis: Formula demonstrations and policy lookups
* Dashboard: Executive summary with charts and KPIs

## Business Metrics Included

* Total Policies
* Claim Count
* Claim Rate %
* Written Premium
  n- Earned Premium
* Paid Loss
* Incurred Loss
* Loss Ratio %
* Average Premium
* Average Loss per Claim
* High Risk Share %

## Derived Columns

* Claim_Flag_Label
* Premium
* Earned_Premium
* Paid_Loss
* Incurred_Loss
* Loss_Ratio
* Claim_Status
* Risk_Segment
* Premium_Band
* Claim_Severity

## Key Insights

* Compared premium vs losses across risk segments
* Measured claim incidence and severity mix
* Identified concentration of losses by customer segment
* Built reusable lookup models for policy-level reporting

## Resume Summary

Built an Insurance Bordereaux & Claims Analytics Dashboard in Excel using advanced formulas, KPI reporting, claims segmentation, and business metrics to support pricing, reserving, and portfolio decisions.

---

# SQL SCRIPT

## Create Table

```sql
CREATE TABLE insurance_claims (
    policy_id INT PRIMARY KEY,
    claim_flag INT,
    premium DECIMAL(12,2),
    earned_premium DECIMAL(12,2),
    paid_loss DECIMAL(12,2),
    incurred_loss DECIMAL(12,2),
    loss_ratio DECIMAL(10,4),
    claim_status VARCHAR(50),
    risk_segment VARCHAR(50),
    premium_band VARCHAR(50),
    claim_severity VARCHAR(50)
);
```

## 20 SQL Queries

```sql
-- 1 Total policies
SELECT COUNT(*) AS total_policies FROM insurance_claims;

-- 2 Total claims
SELECT COUNT(*) AS total_claims FROM insurance_claims WHERE claim_flag = 1;

-- 3 Claim rate
SELECT ROUND(100.0 * SUM(claim_flag) / COUNT(*),2) AS claim_rate_pct FROM insurance_claims;

-- 4 Total premium
SELECT SUM(premium) AS total_premium FROM insurance_claims;

-- 5 Total incurred loss
SELECT SUM(incurred_loss) AS total_incurred_loss FROM insurance_claims;

-- 6 Overall loss ratio
SELECT ROUND(SUM(incurred_loss) / NULLIF(SUM(earned_premium),0),4) AS overall_loss_ratio FROM insurance_claims;

-- 7 Policies by risk segment
SELECT risk_segment, COUNT(*) policy_count FROM insurance_claims GROUP BY risk_segment;

-- 8 Loss by risk segment
SELECT risk_segment, SUM(incurred_loss) total_loss FROM insurance_claims GROUP BY risk_segment ORDER BY total_loss DESC;

-- 9 Premium by risk segment
SELECT risk_segment, SUM(premium) total_premium FROM insurance_claims GROUP BY risk_segment;

-- 10 Avg premium by segment
SELECT risk_segment, AVG(premium) avg_premium FROM insurance_claims GROUP BY risk_segment;

-- 11 Claim count by severity
SELECT claim_severity, COUNT(*) claim_count FROM insurance_claims GROUP BY claim_severity;

-- 12 Paid vs incurred loss
SELECT SUM(paid_loss) paid_loss, SUM(incurred_loss) incurred_loss FROM insurance_claims;

-- 13 Open claims count
SELECT COUNT(*) open_claims FROM insurance_claims WHERE claim_status = 'Open Claim';

-- 14 No claim policies
SELECT COUNT(*) no_claim_policies FROM insurance_claims WHERE claim_status = 'No Claim';

-- 15 Highest loss policies
SELECT policy_id, incurred_loss FROM insurance_claims ORDER BY incurred_loss DESC LIMIT 10;

-- 16 Average loss per claim
SELECT AVG(incurred_loss) avg_loss_per_claim FROM insurance_claims WHERE claim_flag = 1;

-- 17 Premium band distribution
SELECT premium_band, COUNT(*) policies FROM insurance_claims GROUP BY premium_band;

-- 18 Segment loss ratio
SELECT risk_segment,
ROUND(SUM(incurred_loss)/NULLIF(SUM(earned_premium),0),4) AS loss_ratio
FROM insurance_claims GROUP BY risk_segment;

-- 19 High risk share of losses
SELECT ROUND(
100.0 * SUM(CASE WHEN risk_segment='High Risk' THEN incurred_loss ELSE 0 END) / NULLIF(SUM(incurred_loss),0),2
) AS high_risk_loss_share_pct
FROM insurance_claims;

-- 20 Records with zero losses
SELECT COUNT(*) zero_loss_records FROM insurance_claims WHERE incurred_loss = 0;
```
