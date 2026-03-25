# NovaMart Power BI Report - Build Guide

This guide provides step-by-step instructions to build a professional Power BI report from the `NovaMart_Analysis.xlsx` workbook.

## 1. Data Import & Modeling
1. Open Power BI Desktop.
2. **Get Data** -> **Excel Workbook** -> select `NovaMart_Analysis.xlsx`.
3. Choose **Raw Data** and select **Transform Data** to open Power Query.
4. **Rename** query to `Sales`.
5. **Create Date Table**: In the Modeling tab, select **New Table** and use this DAX:
   ```dax
   DateTable = 
   CALENDAR(
       DATE(2022, 1, 1), 
       DATE(2024, 12, 31)
   )
   ```
6. **Relationships**: In the Model View, drag `DateTable[Date]` to `Sales[Date]` (1-to-Many).

---

## 1.1 Troubleshooting "Zero Values"
If your Revenue, Cost, and Profit columns show `0` after import, it's because the Excel formulas haven't been calculated yet. 
**Fix in Power Query:**
1. Click **Transform Data**.
2. Select the **Sales** table.
3. Delete the existing `Revenue`, `Cost`, and `Profit` columns.
4. Go to **Add Column** -> **Custom Column**.
5. Re-create them using:
   - `Revenue` = `[Units_Sold] * [Unit_Price] * (1 - [Discount_Pct])`
   - `Cost` = `[Units_Sold] * [Unit_Price] * 0.65`
   - `Profit` = `[Revenue] - [Cost]`

## 1.2 Data Type Configuration
Ensure your new columns are numeric:
1. In the **Data** pane, click the column (e.g., Revenue).
2. Go to **Column tools** at the top.
3. Set **Data type** to **Decimal Number**. (If it's set to Text, your charts will show errors!)


---

## 2. DAX Measures
Create the following measures in the `Sales` table:

1. **Total Revenue**
   ```dax
   Total Revenue = SUM(Sales[Revenue])
   ```

2. **Total Profit**
   ```dax
   Total Profit = SUM(Sales[Profit])
   ```

3. **Profit Margin %**
   ```dax
   Profit Margin % = DIVIDE([Total Profit], [Total Revenue], 0)
   ```

4. **YoY Revenue Growth**
   ```dax
   YoY Revenue Growth = 
   VAR PriorYear = CALCULATE([Total Revenue], SAMEPERIODLASTYEAR(DateTable[Date]))
   RETURN DIVIDE([Total Revenue] - PriorYear, PriorYear, 0)
   ```

5. **Running Total Revenue**
   ```dax
   Running Total Revenue = 
   CALCULATE(
       [Total Revenue], 
       FILTER(
           ALLSELECTED(DateTable),
           DateTable[Date] <= MAX(DateTable[Date])
       )
   )
   ```

---

## 3. Report Pages

### Page 1: Executive Summary
- **Visuals**:
  - KPI Cards for Total Revenue, Total Profit, and Profit Margin.
  - Line Chart: `Total Revenue` by Month.
  - Gauge Chart: `Total Profit` vs a Target (e.g., $1M).

### Page 2: Sales Breakdown
- **Visuals**:
  - Matrix: Rows (Region, Category), Values (Total Revenue, YoY Growth).
  - Clustered Bar Chart: `Total Revenue` by Sub-Category.
  - Slicer: `Region`.

### Page 3: Product Analysis
- **Visuals**:
  - Funnel Chart: Top 10 Products by Revenue.
  - Table: Product Name, Units Sold, Profit Margin, Revenue.
  - Slicer: `Category`.

### Page 4: Time Intelligence
- **Visuals**:
  - Area Chart: `Running Total Revenue` by Date.
  - Multi-row Card: Today’s Revenue, MTD, QTD, YTD (Create these using DAX like `TOTALYTD`).

---

## 4. Final Polish
1. **Theme**: Apply the "Executive" or "Bloom" theme from the View tab.
2. **Slicers**: Add a Year/Quarter slicer at the top of every page (Sync Slicers).
3. **Publish**: Save as `NovaMart_Report.pbix` and click **Publish** to share it on the Power BI Service.
