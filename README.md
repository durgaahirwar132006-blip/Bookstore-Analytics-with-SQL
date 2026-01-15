# 📚 Bookstore Analytics with SQL

![SQL](https://img.shields.io/badge/SQL-Advanced-blue.svg)
![Database](https://img.shields.io/badge/Database-SQLite-green.svg)
![Analytics](https://img.shields.io/badge/Analytics-Business-orange.svg)
![Status](https://img.shields.io/badge/Status-Complete-success.svg)

## 📌 Project Overview

This project demonstrates comprehensive bookstore analytics using SQL to understand customer preferences, product trends, inventory health, and marketing performance in a retail bookstore environment. The analysis answers critical business questions like which books sell the most, which customers bring the most profit, and how to optimize inventory management.

**Project completed as part of the GeeksforGeeks Data Analysis Certification Program**

## 🎯 Project Objectives

By the end of this project, you will be able to:

- ✅ **Identify best-selling books** and revenue drivers
- ✅ **Track and alert** on low inventory stock
- ✅ **Segment customers** by behavior (RFM Analysis)
- ✅ **Measure ROI** on customer acquisition
- ✅ **Present insights** to stakeholders with data-driven recommendations

## 🛠 Tools and Technologies

### SQL Technologies Used:
- **SQL (SQLite/PostgreSQL/MySQL)** - Primary query language
- **Subqueries** - Nested queries for complex analysis
- **Joins** - Combining data from multiple tables
- **Aggregations** - GROUP BY, SUM, AVG, COUNT
- **Common Table Expressions (CTEs)** - WITH clause for readability
- **Window Functions** - RANK(), ROW_NUMBER(), PARTITION BY
- **Date Functions** - Date manipulation and analysis

### Development Environment:
- **SQLite** - Lightweight, serverless SQL engine (perfect for learning)
- **DB Browser for SQLite** - Visual database tool
- **Online SQL Platforms** - SQLite Online, DB Fiddle
- **Text Editor** - VS Code, Sublime Text

## 📊 Database Schema

The bookstore database includes **4 key tables** that form a robust relational database:

### Entity Relationship Diagram

```
┌─────────────┐         ┌──────────────┐
│   Books     │         │  Customers   │
├─────────────┤         ├──────────────┤
│ book_id (PK)│         │customer_id(PK)│
│ title       │         │ name         │
│ author      │         │ email        │
│ genre       │         │ city         │
│ price       │         │ signup_date  │
│ stock_qty   │         └──────┬───────┘
└──────┬──────┘                │
       │                       │
       │    ┌──────────────────┴─────────┐
       │    │                            │
       │    ▼                            ▼
       │ ┌─────────────┐         ┌──────────────────┐
       └►│   Orders    │         │ MarketingSpend   │
         ├─────────────┤         ├──────────────────┤
         │ order_id(PK)│         │ customer_id (FK) │
         │ customer_id │         │ channel          │
         │ book_id     │         │ cost             │
         │ quantity    │         │ date             │
         │ order_date  │         └──────────────────┘
         └─────────────┘
```

### Table Descriptions

| Table | Description | Key Columns |
|-------|-------------|-------------|
| **Books** | Contains book details and stock | book_id, title, author, genre, price, stock_qty |
| **Customers** | Customer info with city and signup date | customer_id, name, email, city, signup_date |
| **Orders** | Purchase data linking books and customers | order_id, customer_id, book_id, quantity, order_date |
| **MarketingSpend** | Cost to acquire each customer | customer_id, channel, cost, date |

## 📁 Project Structure

```
Bookstore-Analytics-SQL/
│
├── database/
│   ├── schema.sql                  # Database creation script
│   ├── sample_data.sql             # Sample data inserts
│   ├── bookstore.db                # SQLite database file
│   └── README.md                   # Database documentation
│
├── queries/
│   ├── 01_best_sellers.sql         # Top selling books analysis
│   ├── 02_inventory_alerts.sql     # Low stock monitoring
│   ├── 03_customer_segmentation.sql # RFM analysis
│   ├── 04_roi_analysis.sql         # Marketing ROI
│   ├── 05_genre_performance.sql    # Genre trends
│   ├── 06_customer_lifetime_value.sql
│   ├── 07_monthly_sales_trends.sql
│   └── README.md                   # Query documentation
│
├── analysis/
│   ├── analysis_results.md         # Key findings and insights
│   ├── business_recommendations.md # Actionable recommendations
│   └── query_outputs/              # Query result screenshots
│
├── visuals/
│   ├── database_schema.png         # ER diagram
│   ├── sales_trends.png            # Visualizations
│   ├── customer_segments.png
│   └── inventory_dashboard.png
│
├── certificates/
│   └── README.md                   # Certification info
│
├── README.md                       # This file
├── SETUP_GUIDE.md                  # Installation instructions
├── QUERY_GUIDE.md                  # SQL query explanations
└── LICENSE
```

## 🔍 Key Analysis Performed

### 1️⃣ Best-Selling Books & Revenue Drivers

**Business Question:** Which books generate the most revenue?

**SQL Techniques Used:**
- JOIN operations (Orders + Books)
- Aggregations (SUM, COUNT)
- ORDER BY and LIMIT
- Revenue calculation

**Query:**
```sql
SELECT 
    b.title,
    b.author,
    b.genre,
    COUNT(o.order_id) as total_orders,
    SUM(o.quantity) as units_sold,
    SUM(o.quantity * b.price) as total_revenue
FROM Books b
JOIN Orders o ON b.book_id = o.book_id
GROUP BY b.book_id, b.title, b.author, b.genre
ORDER BY total_revenue DESC
LIMIT 10;
```

### 2️⃣ Low Inventory Stock Alerts

**Business Question:** Which books need restocking?

**SQL Techniques Used:**
- WHERE clause filtering
- Threshold-based alerts
- ORDER BY for prioritization

**Query:**
```sql
SELECT 
    book_id,
    title,
    author,
    stock_qty,
    CASE 
        WHEN stock_qty = 0 THEN 'OUT OF STOCK'
        WHEN stock_qty < 5 THEN 'CRITICAL'
        WHEN stock_qty < 10 THEN 'LOW'
        ELSE 'ADEQUATE'
    END as stock_status
FROM Books
WHERE stock_qty < 10
ORDER BY stock_qty ASC;
```

### 3️⃣ Customer Segmentation (RFM Analysis)

**Business Question:** Who are our most valuable customers?

**RFM Model:**
- **R**ecency - How recently did they purchase?
- **F**requency - How often do they purchase?
- **M**onetary - How much do they spend?

**SQL Techniques Used:**
- Window functions (RANK, NTILE)
- CTEs (WITH clause)
- Date calculations
- Complex aggregations

**Query:**
```sql
WITH customer_metrics AS (
    SELECT 
        c.customer_id,
        c.name,
        c.email,
        MAX(o.order_date) as last_purchase_date,
        COUNT(DISTINCT o.order_id) as purchase_frequency,
        SUM(o.quantity * b.price) as total_spent
    FROM Customers c
    JOIN Orders o ON c.customer_id = o.customer_id
    JOIN Books b ON o.book_id = b.book_id
    GROUP BY c.customer_id, c.name, c.email
),
rfm_scores AS (
    SELECT 
        *,
        NTILE(5) OVER (ORDER BY last_purchase_date DESC) as recency_score,
        NTILE(5) OVER (ORDER BY purchase_frequency DESC) as frequency_score,
        NTILE(5) OVER (ORDER BY total_spent DESC) as monetary_score
    FROM customer_metrics
)
SELECT 
    customer_id,
    name,
    recency_score,
    frequency_score,
    monetary_score,
    (recency_score + frequency_score + monetary_score) as rfm_total,
    CASE 
        WHEN (recency_score + frequency_score + monetary_score) >= 12 THEN 'Champions'
        WHEN (recency_score + frequency_score + monetary_score) >= 9 THEN 'Loyal'
        WHEN (recency_score + frequency_score + monetary_score) >= 6 THEN 'Potential'
        ELSE 'At Risk'
    END as customer_segment
FROM rfm_scores
ORDER BY rfm_total DESC;
```

### 4️⃣ Marketing ROI Analysis

**Business Question:** What's the return on our marketing investment?

**SQL Techniques Used:**
- Multiple JOINs
- Revenue vs cost calculation
- ROI formula implementation

**Query:**
```sql
SELECT 
    m.channel,
    COUNT(DISTINCT m.customer_id) as customers_acquired,
    SUM(m.cost) as total_marketing_cost,
    SUM(o.quantity * b.price) as total_revenue,
    SUM(o.quantity * b.price) - SUM(m.cost) as net_profit,
    ROUND(
        (SUM(o.quantity * b.price) - SUM(m.cost)) / SUM(m.cost) * 100, 
        2
    ) as roi_percentage
FROM MarketingSpend m
JOIN Customers c ON m.customer_id = c.customer_id
JOIN Orders o ON c.customer_id = o.customer_id
JOIN Books b ON o.book_id = b.book_id
GROUP BY m.channel
ORDER BY roi_percentage DESC;
```

### 5️⃣ Additional Analysis Queries

- **Genre Performance** - Which book categories perform best?
- **Customer Lifetime Value (CLV)** - Expected revenue per customer
- **Monthly Sales Trends** - Seasonal patterns and growth
- **Customer Retention** - Repeat purchase analysis
- **Geographic Analysis** - Sales by city/region
- **Price Sensitivity** - Impact of pricing on sales

## 📈 Key Findings

### 💰 Revenue Insights
- **Top Genre:** [Fiction/Non-Fiction/etc.] generates XX% of revenue
- **Best-Selling Book:** "[Title]" with $XXX in total sales
- **Average Order Value:** $XX.XX
- **Total Revenue:** $XXX,XXX

### 📦 Inventory Health
- **Books Out of Stock:** X items (X% of catalog)
- **Critical Stock Level:** X books need immediate restocking
- **Overstocked Items:** X books with >50 units in stock

### 👥 Customer Segments
- **Champions:** X customers (XX%) - Generate XX% of revenue
- **Loyal Customers:** X customers (XX%)
- **At-Risk Customers:** X customers (XX%) - Need re-engagement

### 📊 Marketing Performance
- **Best Channel:** [Email/Social/etc.] with XXX% ROI
- **Customer Acquisition Cost:** $XX per customer
- **Lifetime Value:** $XXX average per customer

## 💡 Business Recommendations

### 1. Inventory Optimization
✅ **Implement automated reorder alerts** for books below 10 units  
✅ **Stock more of top-selling genres** based on sales data  
✅ **Reduce inventory** of slow-moving books  

### 2. Customer Retention
✅ **Launch loyalty program** for Champions and Loyal segments  
✅ **Re-engagement campaign** for At-Risk customers  
✅ **Personalized recommendations** based on purchase history  

### 3. Marketing Strategy
✅ **Invest more in high-ROI channels** (e.g., Email if > 200% ROI)  
✅ **Reduce spending** on low-performing channels  
✅ **A/B test** new marketing approaches  

### 4. Revenue Growth
✅ **Bundle popular books** for higher average order value  
✅ **Introduce subscription model** for frequent buyers  
✅ **Seasonal promotions** during peak sales months  

## 🚀 Getting Started

### Prerequisites

- **SQLite** (recommended for beginners) - [Download](https://www.sqlite.org/download.html)
- **OR PostgreSQL** - [Download](https://www.postgresql.org/download/)
- **OR MySQL** - [Download](https://www.mysql.com/downloads/)
- **DB Browser for SQLite** (GUI tool) - [Download](https://sqlitebrowser.org/)

### Installation

#### Option 1: Using SQLite (Easiest)

```bash
# 1. Install SQLite (usually pre-installed on Mac/Linux)
sqlite3 --version

# 2. Clone the repository
git clone https://github.com/yourusername/Bookstore-Analytics-SQL.git
cd Bookstore-Analytics-SQL

# 3. Create database and load data
cd database
sqlite3 bookstore.db < schema.sql
sqlite3 bookstore.db < sample_data.sql

# 4. Run queries
sqlite3 bookstore.db < ../queries/01_best_sellers.sql
```

#### Option 2: Using Online SQL Platform

1. Go to [SQLite Online](https://sqliteonline.com/)
2. Copy contents of `database/schema.sql`
3. Click "Run SQL" to create tables
4. Copy contents of `database/sample_data.sql`
5. Run to insert data
6. Start running queries from `queries/` folder

### Running Queries

```bash
# Run individual query
sqlite3 bookstore.db < queries/01_best_sellers.sql

# Interactive mode
sqlite3 bookstore.db
sqlite> .read queries/01_best_sellers.sql
sqlite> .exit

# Save output to file
sqlite3 bookstore.db < queries/01_best_sellers.sql > output.txt
```

## 📚 SQL Concepts Demonstrated

### Basic SQL
- ✅ SELECT, FROM, WHERE
- ✅ GROUP BY, HAVING
- ✅ ORDER BY, LIMIT
- ✅ Aggregate functions (SUM, COUNT, AVG, MAX, MIN)

### Intermediate SQL
- ✅ INNER JOIN, LEFT JOIN
- ✅ Subqueries
- ✅ CASE statements
- ✅ Date functions
- ✅ String functions

### Advanced SQL
- ✅ Common Table Expressions (CTEs)
- ✅ Window functions (RANK, NTILE, PARTITION BY)
- ✅ Complex joins across multiple tables
- ✅ Calculated fields and KPIs
- ✅ Data segmentation techniques

## 🎓 Learning Outcomes

### Skills Demonstrated

✅ **Database Design** - Understanding of relational databases  
✅ **SQL Querying** - Writing complex analytical queries  
✅ **Business Intelligence** - Deriving actionable insights  
✅ **Data Analysis** - RFM analysis, ROI calculations  
✅ **Problem Solving** - Addressing real business questions  
✅ **Documentation** - Clear query explanations and comments  

## 📸 Screenshots

### Database Schema
![Database Schema](visuals/database_schema.png)

### Query Results
![Best Sellers](visuals/best_sellers_output.png)

### Customer Segmentation
![RFM Analysis](visuals/customer_segments.png)

## 🎓 Certification

This project was completed as part of the **GeeksforGeeks Data Analysis Certification Program**.

**Program:** SQL for Data Analysis  
**Institution:** GeeksforGeeks  
**Completion Date:** January 2026  
**Certificate:** https://www.geeksforgeeks.org/certificate/0518d5d13c0b8d5b704a07c467d76ba6

## 👤 Author

**Durga Ahirwar**  
Aspiring Data Analyst

- 📧 Email: durgaahirwar132006@gmail.com
- 💼 LinkedIn: https://www.linkedin.com/in/%20durga-ahirwar-760059389
- 🐙 GitHub: https://github.com/durgaahirwar132006-blip

## 🤝 Contributing

While this is a personal certification project, feedback and suggestions are welcome!

## 📄 License

This project is for educational purposes as part of the GeeksforGeeks certification program.

MIT License - See LICENSE file for details.

## 🙏 Acknowledgments

- **GeeksforGeeks** for the project requirements and guidance
- **SQL community** for best practices and techniques
- **SQLite** for the excellent lightweight database engine

## 📞 Contact

For questions about this project:

- 📧 Email:  durgaahirwar132006@gmail.com
- 🐙 GitHub Issues: https://github.com/durgaahirwar132006-blip
---

<div align="center">

**⭐ If you found this project helpful, please give it a star! ⭐**

Made with 🎯 by Durga Ahirwar

*Powered by SQL & Data Analysis*

</div>

---

## 📊 Project Statistics

![SQL](https://img.shields.io/badge/Queries-20+-brightgreen)
![Tables](https://img.shields.io/badge/Tables-4-blue)
![Complexity](https://img.shields.io/badge/Complexity-Intermediate--Advanced-orange)
![Lines of Code](https://img.shields.io/badge/LOC-500+-red)

---

### 🔗 Quick Links

- [Database Schema](database/schema.sql)
- [Sample Data](database/sample_data.sql)
- [SQL Queries](queries/)
- [Setup Guide](SETUP_GUIDE.md)
- [Query Guide](QUERY_GUIDE.md)

---

**Last Updated:** January 2024
**Status:** ✅ Complete  
**Level:** Intermediate to Advanced SQL

---

## 🌟 Key Features

- ✅ Real-world business scenarios
- ✅ Production-ready SQL queries
- ✅ Comprehensive documentation
- ✅ Step-by-step implementation
- ✅ Best practices demonstrated
- ✅ Industry-standard techniques

Perfect for showcasing SQL skills in job applications! 🚀
