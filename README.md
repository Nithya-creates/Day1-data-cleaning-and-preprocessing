# Day1-data-cleaning-and-preprocessing


# 💸 Financial Fraud Detection Analysis and Feature Engineering

This repository contains the foundational ETL (Extract, Transform, Load) and Feature Engineering steps performed on a simulated dataset of over 6.3 million financial transactions, aimed at preparing the data for a Machine Learning fraud detection model. The processing for this large dataset was conducted using **Excel's Power Query** functionality.

---

## 1. Data Overview

The raw dataset contains simulated transaction data spanning 30 days (744 hours), designed to mimic a real financial system.

### Original Columns Explained

| Column Name | Data Type | Description |
| :--- | :--- | :--- |
| **TimeStep(1 step=1hour)** | Integer | Time step (in hours) of the simulation. (1 step = 1 hour). |
| **type** | Categorical | The type of transaction (e.g., CASH\_IN, TRANSFER). |
| **amount** | Float | The transaction amount in local currency. |
| **nameOrig** | String (C-ID) | Customer ID initiating the transaction ('C' prefix). |
| **oldbalanceOrg** | Float | Balance before the transaction in the sender's account. |
| **newbalanceOrig** | Float | Balance after the transaction in the sender's account. |
| **nameDest** | String (C-ID / M-ID) | Recipient ID. 'C' for Customer, 'M' for Merchant. |
| **oldbalanceDest** | Float | Balance before the transaction in the recipient's account. (**Often 0.0 for Merchants**) |
| **newbalanceDest** | Float | Balance after the transaction in the recipient's account. |
| **isFraud** | Binary (0/1) | **TARGET VARIABLE:** 1 if the transaction is fraudulent, 0 otherwise. |
| **isFlaggedFraud** | Binary (0/1) | 1 if the system internally flagged the transaction. |

---

## 2. Feature Engineering (Derived Columns)

We focused on creating features that capture anomalies and structural information critical for fraud detection. These new columns were created in the Power Query Editor.

| New Column Name | Source | Purpose / Key Insight |
| :--- | :--- | :--- |
| **is\_Destination\_Merchant** | `nameDest` |  indicating if the recipient is a Merchant ('M'). **Crucial for mitigating noise** from the zeroed-out destination balance columns. |
| **Error_in_origin_balance** | `oldbalanceOrg, newbalanceOrig, amount` | Measures the absolute difference between the actual transaction `amount` and the change in the sender's balance: `ABS((oldbalanceOrg - newbalanceOrig) - amount)`. |
| **is_balance_valid** | `Error_in_Origin_Balance` | Binary flag (1/0) marking a transaction where the `Error_in_Origin_Balance` exceeds a tiny threshold (e.g., > 0.001). **A strong indicator of data tampering or fraud mechanism.** |

---

## 3. Key Analytical Findings

Initial EDA (Exploratory Data Analysis) of the 6.3 million transactions revealed two core risks:

1.  **Fraud Concentration:** All fraud occurs exclusively in the **TRANSFER** and **CASH-OUT** transaction types. All other types have a 0% fraud rate.
2.  **Highest Risk Rate:** While **CASH-OUT** has the highest raw number of fraudulent transactions, the **TRANSFER** type has a much higher inherent risk:
    * **TRANSFER Fraud Rate:** **~76.88%**
    * **CASH-OUT Fraud Rate:** **~18.40%**

This project confirms that efforts to model and prevent fraud should be heavily focused on transactions involving **TRANSFERS** and **CASH-OUTS**, particularly those exhibiting an **`is_Balance_valid`** flag.
