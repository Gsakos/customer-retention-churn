# Customer Retention & Churn Analysis

<div align="center">

<h2>Customer Retention & Churn Analysis</h2>
<p><i>Customer Success Analytics · ARR Risk Quantification · Retention Strategy</i></p>

![SQL](https://img.shields.io/badge/SQL-Analysis-336791?style=flat-square&logo=postgresql&logoColor=white)
![Excel](https://img.shields.io/badge/Excel-Advanced-217346?style=flat-square&logo=microsoft-excel&logoColor=white)
![Tableau](https://img.shields.io/badge/Tableau-Dashboard-E97627?style=flat-square&logo=tableau&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-Reporting-F2C811?style=flat-square&logo=powerbi&logoColor=black)
![Status](https://img.shields.io/badge/Status-Complete-057A55?style=flat-square)

</div>

---

## Business Problem

Leadership has no clear view of which customer segments are churning, why they are leaving, or how much ARR is at risk. The customer success team lacks a data-driven framework for prioritizing retention efforts.

This analysis identifies churn patterns by segment, quantifies revenue at risk, and delivers a prioritized retention playbook with estimated recovery value.

---

## Project Overview

| Detail | Info |
|--------|------|
| **Analyst** | George Anastasakos |
| **Date** | June 2024 |
| **Dataset** | 250 customer accounts across 4 segments |
| **Time Period** | Q1 - Q2 2024 |
| **Tools** | Excel, Google Sheets, SQL, Tableau, Power BI |
| **Industry** | B2B SaaS / Subscription Business / Customer Success |

---

## Key Questions Answered

1. What is the overall churn rate and how does it vary by customer segment?
2. Which customers are at risk and what is the total ARR exposure?
3. What are the primary reasons customers are churning?
4. How does health score correlate with churn probability?
5. Which CSMs have the strongest and weakest retention rates?
6. What is the estimated revenue recovery from targeted intervention?

---

## Repository Structure

```
customer-retention-churn/
|
|-- README.md
|
|-- data/
|   |-- customer_records_raw.xlsx          <- 250 raw customer records
|
|-- analysis/
|   |-- Customer_Retention_Analysis.xlsx   <- Full workbook (5 sheets)
|
|-- sql/
|   |-- 01_churn_rate_by_segment.sql       <- Segment-level churn
|   |-- 02_health_score_correlation.sql    <- Health vs churn analysis
|   |-- 03_csm_retention_performance.sql   <- CSM benchmarking
|   |-- 04_arr_at_risk.sql                 <- At-risk ARR quantification
|
|-- docs/
    |-- retention_playbook.md              <- Written recommendations
```

---

## Methodology

### Data Preparation
- Pulled 250 customer records from CRM and customer success platform
- Standardized health score calculation across all segments
- Validated churn status flags and renewal date fields
- Categorized churn reasons into 6 defined buckets
- Derived calculated fields:
  - `Health Score Tier` — Healthy (70-100), Moderate (50-69), At Risk (30-49), Critical (0-29)
  - `ARR at Risk` — flagged accounts with health score below 50
  - `Churn Rate by Segment` — customers lost / total customers per segment
  - `Net Revenue Retention` — including expansion revenue offset

### Analysis Approach
- Segment-level cohort analysis across Enterprise, Mid-Market, SMB, Startup
- Health score correlation with churn probability
- CSM performance benchmarking by retention rate and ARR managed
- Churn reason revenue impact quantification
- Recovery opportunity modeling using conservative save rate assumptions

---

## Key Findings

### Churn Rate by Segment

| Segment | Customers | Churn Rate | ARR at Risk | Avg Health Score |
|---------|-----------|------------|-------------|-----------------|
| Enterprise | ~62 | ~4% | Lowest | ~78/100 |
| Mid-Market | ~62 | ~8% | Moderate | ~65/100 |
| SMB | ~63 | ~14% | High | ~55/100 |
| Startup | ~63 | ~20% | Highest | ~48/100 |

> SMB and Startup segments churn at 3-5x the rate of Enterprise — but Enterprise churn carries significantly higher ARR impact per account.

---

### Health Score vs Churn Rate

```
Health Tier     Score Range    Churn Rate
------------------------------------------------
Healthy         70 - 100       < 3%      (low risk)
Moderate        50 - 69        ~10%      (monitor)
At Risk         30 - 49        ~28%      (intervene)
Critical        0  - 29        ~55%+     (urgent action)
```

Health score is the single strongest predictor of churn in this dataset.

---

### Top Churn Reasons — by ARR Lost

| Reason | Priority | Recommended Action |
|--------|----------|--------------------|
| Price / Budget | High | Flexible pricing tiers; annual contract discounts |
| Competitor | High | Win/loss analysis; competitive battlecards |
| Product Fit | High | Improve onboarding; 90-day success program |
| No Engagement | High | Automated re-engagement at 14-day login gap |
| Support Issues | Medium | Escalation protocol; proactive outreach after 3+ tickets |
| Company Closure | Low | Flag in CRM; no retention action needed |

---

### CSM Performance

- Top performing CSM retains significantly more ARR than bottom performer
- Churn rate variance between CSMs suggests process and playbook inconsistency
- Best CSM workflows should be documented and scaled across the team

---

## Strategic Recommendations

| Priority | Finding | Action | Timeline |
|----------|---------|--------|----------|
| High | SMB churning at 14% | Build SMB-specific 90-day onboarding and CSM assignment above $10K ARR | 30 days |
| High | At-risk ARR exposure | Executive Business Reviews for all flagged accounts within 48 hours | Immediate |
| High | Price is top churn driver | Introduce annual discount at renewal; flag price-sensitive accounts 90 days out | 30 days |
| High | Low logins = high churn | Automated re-engagement trigger at 14-day login gap | 2 weeks |
| Medium | CSM performance gap | Build retention playbook from top CSM. Rebalance accounts by risk tier | 45 days |
| Medium | Critical health accounts | Immediate CSM outreach; executive sponsor program for high-value accounts | Immediate |
| Medium | Product fit churn | Improve sales qualification; add product fit assessment to onboarding | 60 days |
| Low | NRR below 110% | Introduce upsell motion at 6-month mark; use feature gaps as upsell triggers | 90 days |

---

## Revenue Recovery Estimate

| Initiative | ARR at Stake | Estimated Recovery | Timeline |
|-----------|-------------|-------------------|----------|
| Re-engage at-risk SMB accounts | High | ~30% save rate | 30 days |
| Executive outreach — at-risk Enterprise | High | ~60% save rate | 14 days |
| Price negotiation for budget-cited accounts | Medium | ~40% save rate | 45 days |
| Re-engagement campaign — low login accounts | Medium | ~25% save rate | 21 days |
| **Total Estimated Recovery** | | **30-40% of at-risk ARR** | **45 days** |

---

## SQL Samples

### Churn Rate by Segment
```sql
SELECT
    segment,
    COUNT(*) AS total_customers,
    SUM(churned) AS churned_count,
    ROUND(SUM(churned) * 100.0 / COUNT(*), 1) AS churn_rate_pct,
    SUM(arr) AS total_arr,
    SUM(CASE WHEN churned = 1 THEN arr ELSE 0 END) AS churned_arr
FROM customers
GROUP BY segment
ORDER BY churn_rate_pct ASC;
```

### Health Score vs Churn Correlation
```sql
SELECT
    CASE
        WHEN health_score >= 70 THEN 'Healthy'
        WHEN health_score >= 50 THEN 'Moderate'
        WHEN health_score >= 30 THEN 'At Risk'
        ELSE 'Critical'
    END AS health_tier,
    COUNT(*) AS customers,
    SUM(churned) AS churned,
    ROUND(SUM(churned) * 100.0 / COUNT(*), 1) AS churn_rate_pct,
    SUM(arr) AS total_arr
FROM customers
GROUP BY health_tier
ORDER BY churn_rate_pct ASC;
```

### CSM Retention Performance
```sql
SELECT
    csm_name,
    COUNT(*) AS total_accounts,
    SUM(churned) AS accounts_lost,
    ROUND(SUM(churned) * 100.0 / COUNT(*), 1) AS churn_rate_pct,
    SUM(arr) AS total_arr_managed,
    SUM(CASE WHEN churned = 0 THEN arr ELSE 0 END) AS arr_retained,
    RANK() OVER (ORDER BY SUM(churned) * 1.0 / COUNT(*) ASC) AS retention_rank
FROM customers
GROUP BY csm_name
ORDER BY churn_rate_pct ASC;
```

### At-Risk ARR by Segment
```sql
SELECT
    segment,
    COUNT(*) AS at_risk_accounts,
    SUM(arr) AS at_risk_arr,
    ROUND(AVG(health_score), 1) AS avg_health_score
FROM customers
WHERE health_score < 50 AND churned = 0
GROUP BY segment
ORDER BY at_risk_arr DESC;
```

---

## Tools Used

- **Excel / Google Sheets** — Data cleaning, cohort analysis, KPI dashboard
- **SQL** — Churn rate queries, health score analysis, CSM benchmarking
- **Tableau / Power BI** — Executive retention dashboard *(link coming soon)*

---

## About the Analyst

**George Anastasakos** — Data Analyst with a background in sales operations, CRM systems, and lead generation. I specialize in translating customer and revenue data into retention strategies that protect ARR and drive growth.

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/george-anastasakos-010966408/)
[![GitHub](https://img.shields.io/badge/GitHub-Portfolio-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/Gsakos)

---

<div align="center">
<i>Built with precision. Driven by data. Focused on results.</i>
</div>
