
# Telco Customer Churn Analysis (Power BI)

An interactive Power BI report that shows **why telecom customers leave, which segments are most at risk, and what it costs the business**. Built on the public Telco Customer Churn dataset (7,043 customers).

![Home page](https://github.com/Michael-Emmanuel01/Telco-Customer-Churn-Analysis/blob/main/POWER%20BI(HOME%20PAGE).jpg)

## Table of contents

- [Project overview](#project-overview)
- [Business problem and objectives](#business-problem-and-objectives)
- [Headline numbers](#headline-numbers)
- [Dataset](#dataset)
- [Tools used](#tools-used)
- [Repository structure](#repository-structure)
- [Data preparation](#data-preparation)
- [Data model and DAX measures](#data-model-and-dax-measures)
- [Dashboard walkthrough](#dashboard-walkthrough)
- [Key insights](#key-insights)
- [Recommendations](#recommendations)
- [Limitations and next steps](#limitations-and-next-steps)
- [How to open the project](#how-to-open-the-project)
- [Author](#author)

## Project overview

Customer churn is expensive: winning a new customer usually costs more than keeping an existing one. This project analyses a telecom customer base to find what separates customers who stay from those who leave, and presents the answer in a three-page interactive dashboard that non-technical users can filter.

## Business problem and objectives

**Business question:** Why are customers churning, and what is it costing us?

**Objectives**

1. Measure overall churn and the revenue impact of lost customers.
2. Identify the customer segments and service features linked to churn.
3. Present the findings in an interactive dashboard with slicers and drill-down.
4. Recommend practical retention actions backed by the data.

## Headline numbers

| KPI | Value |
| --- | --- |
| Customers | 7,043 |
| Churned customers | 1,869 |
| Churn rate | 26.5% |
| Monthly revenue lost (churned customers) | 139.13K |
| Total revenue | 16.06M |
| Average monthly charge (churned customers) | 74.44 |
| Average tenure of churned customers | 17.98 months |

## Dataset

- **Source:** Telco Customer Churn dataset
- **Size:** 7,043 rows, one per customer [confirm column count]
- **Target column:** `Churn` (Yes / No)

| Group | Columns |
| --- | --- |
| Identifier | CustomerID |
| Demographics | Gender, Senior_Citizen, Partner, Dependents |
| Services | Phone_Service, Multiple_Lines, Internet_Service, Online_Security, Online_Backup, Device_Protection, Tech_Support, Streaming_TV, Streaming_Movies |
| Account | Contract, Paperless_Billing, Payment_Method, Tenure, Monthly_Charges, Total_Charges |

## Tools used

- **Power BI Desktop:** data model, DAX measures, report design and navigation
- **Power Query:** data cleaning and feature engineering
- **DAX:** KPI and segment measures

## Repository structure

```
telco-customer-churn-powerbi/
├── README.md
├── data/
│   └── Telco_Customer_Churn_Dataset.csv
├── powerbi/
│   └── Churn_Analysis.pbix
├── screenshots/
│   ├── home.png
│   ├── overview.png
│   └── drilldown.png
└── docs/
    ├── Project_Documentation.pdf
    └── Dashboard_Export.pdf
```

## Data preparation

All cleaning was done in Power Query so the steps are visible and repeat on refresh.

| Issue | Action |
| --- | --- |
| Blank or space-only values in `Total_Charges` (new customers with tenure 0) | Trimmed text, replaced empty values with null then 0, changed type to Decimal Number |
| Inconsistent column names | Renamed columns to a clear standard (e.g. `CustomerID`, `Gender`) |
| Wrong data types | Set text, whole number and decimal types explicitly |
| Categories such as "No phone service" vs "No" | Reviewed and kept where meaningful |

**Feature engineering**

- `Tenure_Group`: 0-12, 13-24, 25-48 and 49-72 months
- `Tenure_Sort`: 1 to 4, used with Sort by column so the bins display in order
- `Churn Status`: Churned / Retained label for readable legends

All 7,043 customers are kept; blank `Total_Charges` values are set to 0 rather than removing rows.

## Data model and DAX measures

The model is a single flat table (`Telco`) plus a Measures table, so no relationships are needed.

```dax
TOTAL CUSTOMERS=COUNTROWS (Telco Customer Churn Dataset[Customer ID])

 Total CHURNED CUSTOMER = CALCULATE (
COUNTROWS ('Telco Customer Churn Dataset
(2)'),'Telco Customer Churn Dataset
(2)'[Churn] = "YES")

Total UNCHURNED CUSTOMER =
CALCULATE (
 COUNTROWS ('Telco Customer Churn
Dataset (2)'),
 'Telco Customer Churn Dataset
(2)'[Churn] = "NO")

 CHURN RATE % = DIVIDE ([Total CHURNED
CUSTOMER], [TOTAL_NUMBER_OF_CUSTOMERS], 0)

total revenue = SUM ('Telco Customer Churn
Dataset (2)'[Total Charges])

 REVENUE LOST = CALCULATE (
 SUM ('Telco Customer Churn Dataset
(2)'[Monthly Charges]),'Telco Customer
Churn Dataset (2)'[Churn]= "YES"
)

Avg Monthly Charge (Churned) =
CALCULATE (
 AVERAGE ('Telco Customer Churn Dataset
(2)'[Monthly Charges]),'Telco Customer
Churn Dataset (2)'[Churn]="YES"
)

Avg Tenure (Churned) =
CALCULATE (
 AVERAGE ('Telco Customer Churn Dataset
(2)'[Tenure]),'Telco Customer Churn
Dataset (2)'[Churn]="YES"
)


```

> Revenue Lost is based on **monthly** charges of churned customers, while Total Revenue is **lifetime** charges, so the two are not directly comparable.

## Dashboard walkthrough

The report has three pages with one navy and light-blue theme. Red is reserved for churn so the eye goes to the problem first.

### 1. Home

Entry point with the headline KPIs, navigation buttons, key insights and recommended actions.

![Home](https://github.com/Michael-Emmanuel01/Telco-Customer-Churn-Analysis/blob/main/POWER%20BI(HOME%20PAGE).jpg)

### 2. Overview Dashboard

Who churns, at a glance: Paperless Billing and Multiple Lines slicers, KPI row, donuts for gender, dependents, partner and churn status, tenure group columns, and churn by contract type, internet service and payment method.

![Overview](https://github.com/Michael-Emmanuel01/Telco-Customer-Churn-Analysis/blob/main/POWER%20BI(PAGE%201).jpg
)

### 3. Churn Drill-Down

Which segments are worst: slicers for internet service, contract and payment method, KPIs for churn rate, revenue lost, average monthly charge and average tenure, churn rate by segment, and a customer-level table.

![Drill-down](https://github.com/Michael-Emmanuel01/Telco-Customer-Churn-Analysis/blob/main/POWER%20BI(PAGE%202).jpg)

**Interactivity:** synced slicers across pages, page navigation buttons, Home and Back buttons.

## Key insights

| Segment | Churned customers | Churn rate | Reading |
| --- | --- | --- | --- |
| Month-to-month contract | 1,655 | 42.7% | About 89% of all churn |
| One year contract | 166 | 11.3% | Much lower |
| Two year contract | 48 | 2.8% | Lowest |
| Fiber optic internet | 1,297 | 41.9% | Highest of any service |
| DSL internet | 459 | 19.0% | Moderate |
| Electronic check payment | 1,071 | 45.3% | Highest of any method |
| Automatic payment (bank or card) | 258 / 232 | 16.7% / 15.2% | Low |
| Tenure 0-12 months | 2186 | 47% | Riskiest period |
| Tenure 49-72 months | 2239 | 10% | Most loyal |
| Gender (female / male) | 930 / 939 | similar | Not a driver |



**In short**

1. Month-to-month customers are the core problem: 1,655 of 1,869 churned customers.
2. Fiber optic customers churn at more than twice the rate of DSL customers, pointing to price or service quality.
3. Electronic check payers churn far more than customers on automatic payments.
4. Nearly half of customers in their first 12 months leave.
5. Gender has almost no effect on churn.

## Recommendations

1. **Contracts:** offer discounts or perks that move month-to-month customers onto one- or two-year plans.
2. **Fiber optic:** review pricing, service quality and support.
3. **Payments:** give a small incentive to switch from electronic check to automatic payment.
4. **Onboarding:** add a check-in programme for the first 12 months.
5. **Bundles:** offer tech support and online security to at-risk customers.


## Author

**Michael Emmanuel**

- LinkedIn: [www.linkedin.com/in/michael-emmanuel-94758b292]
- Email: [michaelemmanuel685@gmail.com]
