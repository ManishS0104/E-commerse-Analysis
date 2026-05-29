Vishwajeet Ecommerce Dataset

A small, synthetic ecommerce dataset for practicing data analysis, visualization, and SQL/Pandas. It contains order line items with financials plus a lookup of order metadata (customer, date, and location) for 2018.

Files
• Vishwajeet Ecommerce Data Details.csv
  - Line-level transaction facts.
  - Columns:
    - Order ID: unique order identifier; repeats when multiple line items exist.
    - Amount: line item revenue (numeric).
    - Profit: line item profit (can be negative).
    - Quantity: units sold in the line item.
    - Category: high-level product category (Electronics, Furniture, Clothing).
    - Sub-Category: product subcategory (e.g., Phones, Chairs, Saree).
    - PaymentMode: payment method (Credit Card, COD, UPI, EMI, Debit Card).

• Vishwajeet Ecommerce Orders.csv
  - Order header/lookup info.
  - Columns:
    - Order ID: key to join with details.
    - Order Date: dd-mm-yyyy (string).
    - CustomerName: pseudonymous customer name.
    - State: Indian state/UT.
    - City: customer city.

Row counts and keys
• Many-to-one: multiple detail rows can map to a single order in Orders.csv.
• Primary key suggestion:
  - Details: composite (Order ID, Category, Sub-Category, PaymentMode, an optional RowID if you create one).
  - Orders: Order ID (unique).

Getting started
Python (Pandas)

``python
import pandas as pd

details = pd.readcsv("Vishwajeet Ecommerce Data Details.csv")
orders = pd.readcsv("Vishwajeet Ecommerce Orders.csv", dayfirst=True, parsedates=["Order Date"])

Clean column names (optional)
details.columns = details.columns.str.strip().str.replace(" ", "")
orders.columns = orders.columns.str.strip().str.replace(" ", "")

Join
df = details.merge(orders, on="OrderID", how="left")

Basic metrics
summary = {
    "orders": orders["OrderID"].nunique(),
    "lines": len(details),
    "revenue": details["Amount"].sum(),
    "profit": details["Profit"].sum(),
    "avgordervalue": df.groupby("OrderID")["Amount"].sum().mean()
}
print(summary)

Example: category performance by state
catstate = df.groupby(["State","Category"], asindex=False).agg(
    Revenue=("Amount","sum"),
    Profit=("Profit","sum"),
    Quantity=("Quantity","sum")
).sortvalues(["State","Revenue"], ascending=[True,False])
`

SQL (SQLite example)

`sql
-- Assume two tables: details, orders

SELECT
  o.State,
  o.City,
  d.Category,
  SUM(d.Amount) AS Revenue,
  SUM(d.Profit) AS Profit,
  SUM(d.Quantity) AS Units
FROM details d
LEFT JOIN orders o USING (OrderID)
GROUP BY o.State, o.City, d.Category
ORDER BY Revenue DESC;
`

Suggested analyses
• Sales and profit by category/sub-category.
• Payment mode mix and its profitability.
• State/city heatmap of revenue and margin.
• Top/bottom products by profit and by return-like signals (negative profit).
• Order-level metrics: AOV, items per order, profit per order.
• Monthly trend (parse Order Date) for seasonality.

Data notes
• Dates are in dd-mm-yyyy; parse with dayfirst=true.
• Currency units are not specified; treat “Amount” as revenue, “Profit” as net profit.
• Negative profits represent returns/discounts/losses in this synthetic data.
• Customer names are fictitious; locations reflect Indian states and cities.

License
• This dataset is provided for learning and demonstration purposes. If you use it in blog posts, talks, or repositories, include attribution to “Vishwajeet Ecommerce Dataset (synthetic).”

Citation

Vishwajeet Ecommerce Dataset — Orders and Order Details (2018), synthetic sample for analytics practice.

Contributing
• Issues and PRs to improve documentation, add notebooks, or fix data quality notes are welcome.
• Keep the dataset structure and column names intact; propose additions as separate files or notebooks.

Repository structure (example)

`
.
├─ data/
│  ├─ Vishwajeet Ecommerce Data Details.csv
│  └─ Vishwajeet Ecommerce Orders.csv
├─ notebooks/
│  └─ 01_exploration.ipynb
├─ sql/
│  └─ examples.sql
└─ README.md
``
