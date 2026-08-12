# Financial Fraud & Transaction Behavior Analytics — Technical & Business Documentation

---

## 1. Project Overview & Business Problem

Banks and financial institutions process hundreds of thousands of transactions daily[cite: 4]. Manual fraud detection methods are inefficient, reactive, and incapable of identifying complex operational risks and anomalies in real time[cite: 4].

### Project Objective
The primary goal of this end-to-end data analytics solution is to provide a multi-layered monitoring framework that evaluates:
* **Fraud Exposure & Risk Segmentation:** Isolating systemic fraud patterns from random data noise[cite: 4].
* **Transactional Dynamics:** Tracking daily volumes, moving averages, and temporal patterns[cite: 4].
* **Channel & Behavioral Trends:** Mapping customer preferences across physical and digital payment endpoints[cite: 4].
* **Actionable Risk Mitigation:** Translating statistical findings into concrete business recommendations[cite: 4].

---

## 2. Core Executive KPIs

| Metric | Value | Description |
| :--- | :--- | :--- |
| **Total Volume Analyzed** | **₹9,907,591,425** (~₹9.9B) | Total financial value processed across all channels[cite: 4]. |
| **Total Transactions** | **199,999** (~200K) | Total volume of transactional logs evaluated[cite: 4]. |
| **Systemic Fraud Baseline** | **5.04%** | Average background fraud rate across the platform[cite: 4]. |
| **Total Fraud Exposure** | **₹497,115,710** (~₹497M) | Total monetary value lost to confirmed fraudulent activity[cite: 4]. |
| **Confirmed Fraud Cases** | **10,088** | Total count of identified malicious transactions[cite: 4]. |
| **Average Transaction Value**| **₹49,538** | Mean transaction size across all account types[cite: 4]. |

---

## 3. Enterprise Data Architecture & Star Schema

The data warehouse is engineered using a **Star Schema** centered on `Fact_Transactions` to ensure fast query aggregation, low latency, and efficient relational filtering[cite: 4].

```text
                          +------------------------+
                          |   Dim_Account_Type     |
                          +-----------+------------+
                                      |
+----------------------+              |              +-------------------------+
|     Dim_Geography    +--------------+--------------+        Dim_Date         |
+----------------------+              |              +-------------------------+
                                      |
+----------------------+     +--------+-------+      +-------------------------+
|   Dim_Demographics   +-----+Fact_Transactions+-----+     Dim_Device_Type     |
+----------------------+     +--------+-------+      +-------------------------+
                                      |
+----------------------+              |              +-------------------------+
| Dim_Merchant_Category+--------------+--------------+  Dim_Transaction_Device |
+----------------------+              |              +-------------------------+
                                      |
                          +-----------+------------+
                          |  Dim_Transaction_Type  |
                          +------------------------+
```

### Table Definitions
* **`Fact_Transactions` (Fact Table):** Stores numerical transaction metrics (`Transaction_Amount`, `Account_Balance`, `Hour`), surrogate keys, and binary flags (`Is_Fraud`, `Is_Weekend`)[cite: 4].
* **8 Dimension Tables:** `Dim_Geography`, `Dim_Date`, `Dim_Demographics`, `Dim_Device_Type`, `Dim_Transaction_Device`, `Dim_Merchant_Category`, `Dim_Transaction_Type`, and `Dim_Account_Type`[cite: 4].

---

## 4. ETL & Advanced Feature Engineering

To eliminate arbitrary risk thresholds, dynamic economic banding was developed in **Power Query (M-Code)** using percentile-based distribution logic[cite: 4].

### Dynamic Percentile Algorithm (`Amount_Band`)
The engine dynamically parses transaction amounts to calculate the 25th ($Q_1$), 50th ($Q_2$), and 75th ($Q_3$) percentiles[cite: 4]:

```powerquery
let
    Source = #"Previous_Step",
    AmountList = Source[Transaction_Amount],
    Q1 = List.Percentile(AmountList, 0.25),
    Q2 = List.Percentile(AmountList, 0.50),
    Q3 = List.Percentile(AmountList, 0.75),
    
    #"Added Amount Band" = Table.AddColumn(
        Source, 
        "Amount_Band", 
        each if [Transaction_Amount] <= Q1 then "Low"
             else if [Transaction_Amount] <= Q2 then "Medium"
             else if [Transaction_Amount] <= Q3 then "High"
             else "Very High", 
        type text
    )
in
    #"Added Amount Band"
```

---

## 5. Statistical Rigor & Sample Bias Mitigation

Relying purely on raw percentages introduces **small-sample bias** (e.g., a merchant category with 2 out of 3 fraudulent transactions shows a deceptively high 66.7% rate)[cite: 4]. To prevent false positives, the **Wilson Score Interval** algorithm was implemented using **DAX Measures**[cite: 4].

### Mathematical Formulation
The Wilson Center ($\tilde{p}$) and Interval Bounds adjust the observed sample proportion ($\hat{p}$) based on sample size ($n$) and standard normal distribution score ($z$)[cite: 4]:

$$\text{Wilson Center} = \frac{\hat{p} + \frac{z^2}{2n}}{1 + \frac{z^2}{n}}$$

$$\text{Wilson Margin} = \frac{z}{1 + \frac{z^2}{n}} \sqrt{\frac{\hat{p}(1-\hat{p})}{n} + \frac{z^2}{4n^2}}$$

$$\text{Lower Bound} = \text{Center} - \text{Margin}, \quad \text{Upper Bound} = \text{Center} + \text{Margin}$$

### DAX Measure Directory
* **Core Metrics:** `Total Transactions`, `Total Fraud Count`, `Total Legitimate Count`, `Fraud Exposure`, `Fraud Rate %`[cite: 4].
* **Statistical Bounds:** `Baseline Fraud Rate` (5.04%), `Wilson Center`, `Wilson Margin`, `Wilson Z Value`, `Segment Fraud Rate - Lower Bound`, `Segment Fraud Rate - Upper Bound`[cite: 4].
* **Significance Evaluation:** `Is Segment Significant vs Baseline` (Returns TRUE only if the Lower Bound exceeds 5.04%)[cite: 4].
* **Time Intelligence:** `7-Day Moving Avg Transaction Volume`[cite: 4].

---

## 6. Dashboard Architecture & Key Insights

The solution features three interconnected interactive dashboards[cite: 4]:

### Dashboard 1: Executive Overview
* **Smoothed Trend Analysis:** Overlays a **7-Day Moving Average** on top of daily volumes to filter out daily volatility[cite: 4].
* **Geographic Volume Leaders:**
  * **Chandigarh:** ₹393M[cite: 4]
  * **Kavaratti:** ₹285M[cite: 4]
  * **Udaipur:** ₹127M[cite: 4]
* **Balanced Portfolio Mix:** Account types show equal system distribution: Checking (33.50%), Savings (33.27%), and Business (33.23%)[cite: 4].

### Dashboard 2: Fraud Monitoring & Statistical Analysis
* **High-Risk Sector Identification:** Clothing (5.20%) and Groceries (5.19%) cross the upper control limit of the 5.04% baseline[cite: 4].
* **Monetary Exposure Concentration:** The **Very High** amount band represents the highest exposure at **₹209.47M** across 2,415 cases[cite: 4].
* **Coordinated Attack Windows:** Identified synchronized fraud rate spikes on **January 6 (5.75%)** and **January 26 (5.58%)**, pointing to automated botnet exploits[cite: 4].

### Dashboard 3: Transaction & Channel Behavior
* **Temporal Demand:** Peak transactional load occurs daily between **12:00 PM and 3:00 PM**[cite: 4].
* **Weekly Split:** **72.26%** of interactions occur on Weekdays vs. **27.74%** on Weekends[cite: 4].
* **Infrastructure Traffic:** Density heatmaps highlight **ATMs and Self-Service Kiosks** as primary physical touchpoints (>5,100 transactions/cell)[cite: 4].

---

## 7. Strategic Action Plan

```text
+-----------------------------------------------------------------------------------+
|                            STRATEGIC RECOMMENDATIONS                              |
+-----------------------------------------------------------------------------------+
| 1. Enforce Step-up Authentication (2FA / 3DS):                                    |
|    Mandate 3D Secure verification loops for High/Very High transactions exceeding |
|    ₹50K in elevated merchant sectors (Clothing & Groceries).                      |
+-----------------------------------------------------------------------------------+
| 2. API Forensic Investigation:                                                    |
|    Conduct deep-packet network log audits for Jan 6 and Jan 26 anomaly windows to |
|    patch automated botnet credential-stuffing exploits.                           |
+-----------------------------------------------------------------------------------+
| 3. Infrastructure Load Balancing:                                                 |
|    Schedule routine database updates and ATM server maintenance around 9:00 AM,   |
|    aligning with daily off-peak traffic windows.                                  |
+-----------------------------------------------------------------------------------+
```

---

## 8. Technical Stack

* **Database & Data Pipeline:** SQL (MySQL)[cite: 4]
* **Scripting & Analytics:** Python, Power Query (M-Code)[cite: 4]
* **Visualization & Modeling:** Tableau, Power BI, Advanced DAX[cite: 4]
* **Spreadsheet Utility:** Microsoft Excel[cite: 4]


---

## 🔗 Project Resources & Assets

* 📁 **Full Project Resources (Datasets, Tableau .twbx, Demo Video & Presentation Slides):** [Access Google Drive Folder](https://drive.google.com/drive/folders/1FDKRmjcbCavGXAAUJDMrZmtgLoLhoJbK?usp=sharing)
