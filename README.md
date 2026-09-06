# MoMo Transactions Analysis

## Overview

This project analyses a MoMo transactions dataset using **Python and Pandas**. The analysis focuses on data cleaning, missing-value handling, transaction behaviour, and a product-focused insight that can support decision-making.

The project was completed as a practical data-analysis exercise.

## Objectives

The analysis answers four questions:

1. How many duplicate rows are present, and how should they be handled?
2. How many `session_seconds` values are missing, and what is the appropriate treatment?
3. Which `txn_type` has the biggest gap between its mean and median transaction amount?
4. What insight from the transaction data could influence a product manager's decision?

## Tools & Technologies

* **Python**
* **Pandas** — data cleaning and analysis
* **Matplotlib** — data visualization
* **Jupyter Notebook** — analysis environment

## Data Cleaning

### Duplicate Rows

The dataset contained **60 duplicate rows**.

These records were removed using Pandas:

```python
txn_clean = txn.drop_duplicates().copy()
```

Removing exact duplicate records prevents the same transaction from being counted more than once during the analysis.

### Missing `session_seconds`

After duplicate removal, **421 `session_seconds` values** were missing.

The missing values were replaced with the **median session duration**. Median imputation was selected because session duration can be affected by unusually long sessions, making the median a more robust representation of a typical session.

```python
session_median = txn_clean["session_seconds"].median()

txn_clean["session_seconds"] = (
    txn_clean["session_seconds"].fillna(session_median)
)
```

## Transaction Type Analysis

The mean and median transaction amounts were calculated for each `txn_type`.

```python
txn_type_stats = (
    txn_clean
    .groupby("txn_type")["amount"]
    .agg(["mean", "median"])
)

txn_type_stats["gap"] = (
    txn_type_stats["mean"] - txn_type_stats["median"]
)
```

The transaction type with the **largest mean–median gap was `savings`**, with a gap of approximately **8002.034748322148**.

This suggests that some relatively high-value savings transactions are pulling the average transaction amount considerably above the typical transaction amount.

## Product Insight

### Chart

The analysis uses transaction volume by transaction type to identify where customer activity is concentrated.

```python
txn_counts = txn_clean["txn_type"].value_counts()

plt.figure(figsize=(9, 5))
plt.bar(txn_counts.index, txn_counts.values)
plt.title("Transaction Volume by Transaction Type")
plt.xlabel("Transaction Type")
plt.ylabel("Number of Transactions")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

### Product Decision

**Prioritise product reliability and user-experience improvements around high-volume transaction types, starting with the transaction type with the highest transaction volume.**

This approach allows a product team to focus resources where improvements have the potential to affect the largest number of transactions.

## Key Findings

| Analysis                  | Finding                       |
| ------------------------- | ----------------------------- |
| Duplicate rows            | 60 removed                    |
| Missing `session_seconds` | 421 after deduplication       |
| Missing-value treatment   | Median imputation             |
| Largest mean–median gap   | `savings`                     |
| Mean–median gap           | 8002.034748322148                      |
| Product focus             | High-volume transaction types |

## Project Structure

```text
momo-transactions-analysis/
│
├── momo_transactions.csv
├── momo_transactions_practice_task.ipynb
└── README.md
```

## Conclusion

This analysis demonstrates a simple workflow for turning transaction data into actionable product insights. The cleaning process improves the reliability of the analysis, while transaction-level comparisons reveal differences in customer behaviour across transaction types.

The final product insight uses transaction volume to support prioritisation of product improvements.
