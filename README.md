# Ecommerce TV Data Analysis (SQL)

## Overview

This project demonstrates SQL data analysis on an ecommerce TV sales dataset. The analysis covers a range of SQL concepts including filtering, aggregation, subqueries, views, and indexing, using a table created from `Ecommerce.csv`.

**Database Used:** MySQL (compatible with PostgreSQL/SQLite with minor syntax changes)

**Dataset:** `Ecommerce.csv` (TV sales data)

---

## Dataset Structure

The table `ecommerce` was created from the following columns:

- `Brand`
- `Resolution`
- `Size`
- `Selling Price`
- `Original Price`
- `Operating System`
- `Rating`

---

## SQL Tasks & Queries

### 1. Basic SELECT with WHERE and ORDER BY
**Purpose:** Find all TVs with price less than 20,000 and rating above 4.5, ordered by rating.
```sql
SELECT Brand, Resolution, `Size`, `Selling Price`, Rating
FROM ecommerce
WHERE `Selling Price` < 20000 AND Rating > 4.5
ORDER BY Rating DESC;
```

### 2. GROUP BY with Aggregate Functions
**Purpose:** Calculate average selling price and count of TVs by brand.
```sql
SELECT Brand, 
       COUNT(*) as total_models,
       ROUND(AVG(`Selling Price`), 2) as avg_price,
       ROUND(AVG(Rating), 2) as avg_rating
FROM ecommerce
GROUP BY Brand
HAVING COUNT(*) > 5
ORDER BY avg_price DESC;
```

### 3. Subquery Example
**Purpose:** Find TVs that are priced higher than the average price for their brand.
```sql
SELECT e1.Brand, e1.Resolution, e1.`Size`, e1.`Selling Price`
FROM ecommerce e1
WHERE e1.`Selling Price` > (
    SELECT AVG(e2.`Selling Price`)
    FROM ecommerce e2
    WHERE e2.Brand = e1.Brand
)
ORDER BY e1.Brand, e1.`Selling Price`;
```

### 4. Creating a View for Price Analysis
**Purpose:** Create a view to analyze average prices and discounts by brand and resolution.
```sql
CREATE VIEW price_analysis AS
SELECT 
    Brand,
    Resolution,
    COUNT(*) as model_count,
    ROUND(AVG(`Selling Price`), 2) as avg_selling_price,
    ROUND(AVG(`Original Price`), 2) as avg_original_price,
    ROUND(AVG(`Original Price` - `Selling Price`), 2) as avg_discount
FROM ecommerce
GROUP BY Brand, Resolution;
```

### 5. Complex Query with Multiple Conditions
**Purpose:** Analyze discount percentage by brand and resolution for TVs with ratings > 4.
```sql
SELECT 
    Brand,
    Resolution,
    COUNT(*) as total_models,
    ROUND(AVG(`Selling Price`), 2) as avg_selling_price,
    ROUND(AVG(`Original Price`), 2) as avg_original_price,
    ROUND(((AVG(`Original Price`) - AVG(`Selling Price`))/AVG(`Original Price`) * 100), 2) as discount_percentage
FROM ecommerce
WHERE Rating > 4
GROUP BY Brand, Resolution
HAVING COUNT(*) > 3
ORDER BY discount_percentage DESC;
```

### 6. Size Distribution Analysis
**Purpose:** Analyze TV size categories and their average price and rating.
```sql
SELECT 
    CASE 
        WHEN `Size` <= 32 THEN 'Small (≤32)'
        WHEN `Size` <= 43 THEN 'Medium (33-43)'
        WHEN `Size` <= 55 THEN 'Large (44-55)'
        ELSE 'Extra Large (>55)'
    END as size_category,
    COUNT(*) as total_models,
    ROUND(AVG(`Selling Price`), 2) as avg_price,
    ROUND(AVG(Rating), 2) as avg_rating
FROM ecommerce
GROUP BY size_category
ORDER BY avg_price;
```

### 7. Operating System Market Analysis
**Purpose:** Analyze the popularity and average price/rating of TVs by operating system.
```sql
SELECT 
    `Operating System`,
    COUNT(*) as total_models,
    ROUND(AVG(`Selling Price`), 2) as avg_price,
    ROUND(AVG(Rating), 2) as avg_rating,
    COUNT(DISTINCT Brand) as number_of_brands
FROM ecommerce
WHERE `Operating System` IS NOT NULL
GROUP BY `Operating System`
ORDER BY total_models DESC;
```

### 8. Index Creation for Performance
**Purpose:** Optimize queries filtering by brand and price.
```sql
CREATE INDEX idx_brand_price ON ecommerce(Brand, `Selling Price`);
```
*To view all indexes:*
```sql
SHOW INDEXES FROM ecommerce;
```

### 9. Price Range Distribution
**Purpose:** Analyze the distribution of TVs by price category.
```sql
SELECT 
    CASE 
        WHEN `Selling Price` <= 15000 THEN 'Budget (≤15K)'
        WHEN `Selling Price` <= 30000 THEN 'Mid-Range (15K-30K)'
        WHEN `Selling Price` <= 50000 THEN 'Premium (30K-50K)'
        ELSE 'Luxury (>50K)'
    END as price_category,
    COUNT(*) as total_models,
    ROUND(AVG(Rating), 2) as avg_rating,
    COUNT(DISTINCT Brand) as number_of_brands
FROM ecommerce
GROUP BY price_category
ORDER BY total_models DESC;
```

### 10. Best Value Analysis
**Purpose:** Find TVs with the best price-to-rating ratio.
```sql
SELECT 
    Brand,
    Resolution,
    `Size`,
    `Selling Price`,
    Rating,
    ROUND(`Selling Price`/Rating, 2) as price_per_rating_point
FROM ecommerce
WHERE Rating > 4
ORDER BY price_per_rating_point ASC
LIMIT 10;
```
Screenshots
![Image](https://github.com/user-attachments/assets/b75434c0-6d71-4cf7-b486-cccb02fce95d)
![Image](https://github.com/user-attachments/assets/8a6c9da8-cd43-4c56-b3b3-d2be169c670d)
![Image](https://github.com/user-attachments/assets/6c72c4b4-d403-4733-8769-97d9de2ffa82)
![Image](https://github.com/user-attachments/assets/099f2cdd-733b-4653-9109-e28af344948d)
![Image](https://github.com/user-attachments/assets/d55d06e6-4c07-4445-a29d-ad828ff64433)
![Image](https://github.com/user-attachments/assets/aa326ea1-4703-46c9-829b-c1b80da8a424)
![Image](https://github.com/user-attachments/assets/3fd0ba1d-2f33-4ca0-a529-49db7eeeadcc)
![Image](https://github.com/user-attachments/assets/d50bee3d-c2dd-432f-a1df-cfcbeb081a76)
![Image](https://github.com/user-attachments/assets/67983e31-63fb-46f5-8a80-547665cf891d)
![Image](https://github.com/user-attachments/assets/88055373-17a2-4a38-840e-179614c23187)
---

## How to Use

1. **Import the CSV** into your SQL database as a table named `ecommerce`.
2. **Run the queries** in `ecommerce_task4.sql` using your SQL client.
3. **View outputs** directly in your SQL client or export results as needed.


