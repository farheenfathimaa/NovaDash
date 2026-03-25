# NovaMart Tableau Dashboard - Build Guide

This guide provides step-by-step instructions to build the **NovaMart Performance Dashboard** in Tableau using the `NovaMart_Analysis.xlsx` dataset.

## 1. Connecting to Data
1. Open Tableau Desktop / Public.
2. Under **Connect**, select **Microsoft Excel**.
3. Navigate to and select `NovaMart_Analysis.xlsx`.
4. Drag the **Raw Data** sheet into the canvas.
5. Ensure data types are correct:
   - `Order_Date`: Date
   - `Region`: String (Geographic: Region)
   - `Revenue/Profit`: Decimal (Number)

---

## 1.1 Troubleshooting "Zero Values"
If your fields show `0`, you must create **Calculated Fields** to compute them on the fly:
1. Right-click in the **Data pane** -> **Create Calculated Field**.
2. **Total Revenue**: `[Units Sold] * [Unit Price] * (1 - [Discount Pct])`
3. **Total Cost**: `[Units Sold] * [Unit Price] * 0.65`
4. **Total Profit**: `[Total Revenue] - [Total Cost]`

*Always use these "Total" fields for your visualizations to ensure accurate numbers.*

---

## 2. Visualizations to Create

### 1. Bar Chart: Top 10 Products by Revenue
- **Columns**: `SUM(Revenue)`
- **Rows**: `Product_Name`
- **Steps**:
  - Sort `Product_Name` descending by `Revenue`.
  - Filter `Product_Name` using the **Top** tab (Top 10 by Revenue).
  - Add `Revenue` to **Label** for clarity.

### 2. Map: Revenue by Region
- **Marks**: Map
- **Detail**: `Region`
- **Color**: `SUM(Revenue)`
- **Steps**:
  - If Tableau doesn't recognize your regions, right-click `Region` -> **Geographic Role** -> **State/Province**. 
  - Click the "**5 unknown**" error in the bottom right -> **Edit Locations**.
  - Map your regions to real states (e.g., Central = Madhya Pradesh, North = Delhi, etc.) so the map appears.
  - **Important:** Drag **Longitude (generated)** to Columns and **Latitude (generated)** to Rows!

### 3. Line Chart: Monthly Sales Trend with Forecast
- **Columns**: `MONTH(Date)` (Continuous)
- **Rows**: `SUM(Revenue)`
- **Steps**:
  - Right-click the axis -> **Show Trend Lines**.
  - **Crucial:** Ensure the Date pill in Columns is **Green (Continuous)**. Right-click it and select the second "Month" option in the menu.
  - Go to the **Analytics Pane** -> Drag **Forecast** onto the view.
  - Style: Use a thick line with markers.

### 4. Scatter Plot: Discount % vs Profit Margin
- **Columns**: `AVG(Discount_Pct)`
- **Rows**: `SUM(Profit) / SUM(Revenue)` (Create a Calculated Field: `Profit Margin`).
- **Detail**: `Order_ID`
- **Steps**:
  - Add a **Trend Line** (Linear).
  - To see all 500+ dots: Go to the **Analysis menu** at the top -> **Uncheck "Aggregate Measures"**.
  - *Pro-Tip:* Use **Total Revenue** vs **Total Profit** for a better "scattered" cloud of dots!

### 5. Treemap: Revenue Breakdown
- **Hierarchy**: Drag `Sub_Category` onto `Category` to create a hierarchy.
- **Marks**: Square
- **Size**: `SUM(Revenue)`
- **Color**: `Category`
- **Label**: `Category`, `Sub_Category`, and `SUM(Revenue)`.

---

## 3. Building the Interactive Dashboard
1. Create a new Dashboard (Size: Desktop Browser 1000x800).
2. Drag all 5 sheets onto the dashboard using a **Tiled** or **Floating** layout.
3. **Interactivity (Actions)**:
   - Click the funnel icon (**Use as Filter**) on the **Region Map** and **Category Treemap**.
   - This allows users to click a region or category and see all other charts update dynamically.
4. **Formatting**:
   - Use a Title card: "NovaMart Executive Performance".
   - Add the NovaMart logo (placeholder).
   - Use consistent fonts (Arial or Tableau Book).

---

## 4. Workbook Template Structure (.twbx)
Since this is a markdown guide, here is how you would save your work:
1. `File` -> `Save As`.
2. Select `.twbx` (Tableau Packaged Workbook) to ensure the Excel data is embedded.
