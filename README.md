# 🚚 APEX Logistics & Retail — Supply Chain & Logistics Analytics (Power BI)

![Power BI](https://img.shields.io/badge/Power_BI-F2C94C?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-217346?style=for-the-badge&logo=microsoft-excel&logoColor=white)
![Supply Chain Analytics](https://img.shields.io/badge/Domain-Supply_Chain_&_Logistics-0078D4?style=for-the-badge)

## 📌 Executive Summary
This Power BI analytics project delivers an end-to-end performance audit for **APEX Logistics & Retail**, evaluating supply chain operational friction, shipping delays, and inventory risks across **180,519 total shipments**.

While total generated revenue reached **$36,784,735 ($36.78M)** across 384,079 units sold, the diagnostic analysis uncovered a critical operational bottleneck: an overall **Late Delivery Rate of 55%**, with an average actual delivery duration of **3.50 days** and an additional **1.60 days of overdue delay** beyond scheduled commitments.

---

## 📸 Interactive Dashboard Preview

| Page 1: Executive Overview | Page 2: Logistics & Shipping |
|---|---|
| ![Executive Overview](executive-overview.png) | ![Logistics Performance](logistics-shipping-performance.png) |

| Page 3: Product & ABC Analysis |
|---|
| ![ABC Analysis](product-abc-analysis.png) |

---

## 📊 Dashboard Architecture & Interactive Pages

The Power BI report is structured into three executive-level analytical pages:

### 1️⃣ Executive Overview Page
* **Total Sales:** $36.78M across 65,752 overall tracked orders in the sample.
* **On-Time vs. Late Delivery:** 45% On-Time Delivery Rate vs. 55% Late Delivery Rate.
* **Geographical Sales Distribution:** Europe ($10.9M) and LATAM ($10.3M) lead global market revenues, followed by Pacific Asia ($8.3M), USCA ($5.1M), and Africa ($2.3M).

### 2️⃣ Logistics & Shipping Performance Page
* **Systemic Friction:** Late delivery rates span consistently between **50% and 60%** across all shipping modes (`Standard Class`, `Second Class`, `First Class`, and `Same Day`), confirming a systemic supply chain issue rather than a single-carrier flaw.
* **Shipping Mode Distribution:** `Standard Class` accounts for the vast majority of volume (39K orders), experiencing a **38.13% late rate** alongside a high percentage of advance/canceled shipments.

### 3️⃣ Product & ABC Inventory Analysis Page
* **Revenue Pareto Principle (80/20 Classification):**
  * **Class A SKUs:** Drive **76.9%** of total revenue (~$28.28M) across key items.
  * **Class B SKUs:** Drive **18.1%** of total revenue (~$6.65M).
  * **Class C SKUs:** Drive **5.0%** of total revenue (~$1.85M).
* **High-Risk Product Exposure:** **105 out of 118 unique products** exhibit a Late Delivery Rate exceeding 50%, directly exposing high-margin items to delivery failure risks.

---

## 🛠️ Data Modeling, Calculated Columns & DAX Measures

The data model processes order data via custom calculated columns and key DAX measures for SLA tracking, running Pareto ABC revenue totals, and risk filtration:

### 🔹 Calculated Column (ABC Segmentation Logic)
```dax
ABC Category = 
VAR CurrentProductSales = 
    CALCULATE(
        SUM('DataCoSupplyChainDataset'[Sales]), 
        ALLEXCEPT('DataCoSupplyChainDataset', 'DataCoSupplyChainDataset'[Product Card Id])
    )

VAR TotalCompanySales = 
    CALCULATE(
        SUM('DataCoSupplyChainDataset'[Sales]), 
        ALL('DataCoSupplyChainDataset')
    )

VAR RunningTotalSales = 
    CALCULATE(
        SUM('DataCoSupplyChainDataset'[Sales]),
        FILTER(
            ALL('DataCoSupplyChainDataset'),
            CALCULATE(
                SUM('DataCoSupplyChainDataset'[Sales]), 
                ALLEXCEPT('DataCoSupplyChainDataset', 'DataCoSupplyChainDataset'[Product Card Id])
            ) >= CurrentProductSales
        )
    )

VAR CumulativeRatio = DIVIDE(RunningTotalSales, TotalCompanySales, 0)

RETURN
    SWITCH(
        TRUE(),
        CumulativeRatio <= 0.80, "Class A",
        CumulativeRatio <= 0.95, "Class B",
        "Class C"
    )
