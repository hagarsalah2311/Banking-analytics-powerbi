# Banking Customer & Operations Analytics Dashboard

An interactive Power BI report that gives a retail bank one place to monitor its customers, accounts, transactions, loans, cards and customer support.

**Tools:** Power BI Desktop, Power Query (M), DAX, star-schema data modelling

![Overview](screenshots/01-overview.png)

## Report pages

| Page | What it answers |
|---|---|
| **Overview** | Headline KPIs (customers, transactions, transaction value, balances, loan amount) with year-over-year change, balance and transactions by account type, loan status, issues solved vs unsolved |
| **Customer Insights** | Account holders, active vs dormant accounts, average accounts per customer, account mix by type, customer activity by number of transactions |
| **Transactions** | Transaction count and value by account type, transaction type and over time |
| **Loan Monitoring** | Number of loans, total / largest / smallest loan, average interest rate, loans by type and status |
| **Support & Satisfaction** | Issues raised, solved and unsolved, resolution rate, issues by state, issue type and over time |
| **Tracking Loan start & end date** | Drill-through table of every loan with customer, type, amount, start date, end date and status |

Every page has slicers (year, account type, loan status) and a navigation sidebar.

![Customer Insights](screenshots/02-customer-insights.png)
![Transactions](screenshots/03-transactions.png)
![Loan Monitoring](screenshots/04-loan-monitoring.png)
![Support and Satisfaction](screenshots/05-support-satisfaction.png)

## Data

Six source tables of synthetic sample data (no real customers), plus a date table.

| Table | Rows | Notes |
|---|---|---|
| Customers | 5,050 | Join date, state |
| Accounts | 5,100 | Savings, Checking, Business |
| Transactions | 20,000 | Deposit, Withdrawal, Payment, Transfer (May 2022 to May 2025) |
| Loans | 2,600 | Car, Home, Personal, Education, Business |
| Cards | 4,000 | Debit, Credit, Prepaid |
| SupportCalls | 3,100 | Account Access, Transaction Dispute, Card Issue, Loan Query |
| DimDate | calendar table | Year, quarter, month, day |

## Data model

Star schema, with Customers and Accounts as the main dimensions:

- `Customers` 1 to many `Accounts`, `Loans`, `Cards`, `SupportCalls` (on `CustomerID`)
- `Accounts` 1 to many `Transactions` (on `AccountID`)
- `DimDate` marked as the date table for time intelligence

## Data cleaning (Power Query)

- Found that transaction and join dates were in US month/day format and were being misread by a day/month locale (60% errors, plus silently wrong months). Re-typed the columns with the correct locale.
- Removed index columns and personal fields (phone, email, address, card number) that no KPI needs.
- Trimmed and standardised text, converted interest rates to percentages, and added derived columns (loan status, account age, month and year fields).
- Checked referential integrity with left-anti merges: no orphan accounts, transactions, loans, cards or support calls.

## Key DAX measures

- Customers, transactions, transaction value, balance, loan amount
- Year-over-year variance with `SAMEPERIODLASTYEAR`, plus arrow and colour measures for conditional KPI cards
- **Active Accounts:** accounts with a transaction in the 500 days before the latest transaction date in the data. **Dormant Accounts** = total accounts minus active accounts
- **Issues Solved %:** resolved support calls divided by all calls
- **Loan Status:** loan end date after today means *Active Loan*, otherwise *Ended Loan*

## What the data shows

- Total balances are about **254.7M**, total loans about **644.3M**, and transactions are worth about **100.1M** across 20,000 transactions.
- **49.3%** of support calls are resolved. Calls are spread almost evenly across the four issue types (Transaction Dispute is highest at 803).
- Car, Home, Personal and Education loans are each around 620 to 660 loans and 154M to 162M. **Business loans are a small outlier: 28 loans and 7.1M.**
- About **1,850 customers (37%)** have no account, which is a cross-sell opportunity.
- Transactions are split almost equally across the four types, so no single transaction type dominates.

## Limitations

- The support data has no agent, priority or resolution-time fields, so those analyses are not included.
- The loan data has no income or credit score, so default risk and loan-to-income ratios cannot be calculated.
- Loan status depends on today's date, so it changes as the report is refreshed over time.

## How to open

1. Install [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (free, Windows).
2. Download `sprint_2.pbix` from this repository and open it.

## Author

[Hagar Salah] 
