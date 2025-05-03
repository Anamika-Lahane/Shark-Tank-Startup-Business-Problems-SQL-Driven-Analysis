# Shark Tank India Data Analysis Project

## 📅 Project Overview:

This project aims to analyze startup data from the Indian version of Shark Tank using structured SQL queries. The analysis covers funding trends, investor behavior, startup demographics, valuation patterns, and geographic distributions. The primary goal is to derive actionable insights that can help understand what types of startups receive investments, the sectors with the most traction, and investor preferences.

By using SQL to query the dataset, we have built a range of business intelligence insights that could serve entrepreneurs, investors, analysts, and viewers interested in startup dynamics and Shark Tank trends.

---

## 🌟 Objectives

- ✅ Delivered actionable insights to identify overvalued startups, investor preferences, and funding patterns.
- ✅ Recommended high-performing sectors and presenter profiles to improve pitch success and investor engagement.
- ✅ Highlighted key geographic and demographic trends to inform strategic targeting of startup support initiatives.
- ✅ Ranked top sharks by investment behavior to reveal dominant strategies and investor aggressiveness.

---

## 🧹 Data Cleaning & Preprocessing Summary

- **Python-Based Cleaning:** Used Pandas, NumPy, and datetime to clean and standardize the dataset.
- **Categorical Null Handling:** Filled missing values in categorical fields like `Started_in`, `Pitchers_City`.
- **Boolean Fields:** Standardized missing shark presence columns with "No".
- **Numerical Fields:** Replaced nulls in numerical fields with 0.
- **Revenue & Sales:** Treated missing revenue/sales values as "Not Mentioned".
- **Text Cleanup:** Removed default "0" values in descriptive text fields.
- **MySQL Setup:** Enabled `local_infile=ON` for bulk imports.
- **Data Import:** Used `LOAD DATA INFILE` for loading cleaned CSV into MySQL.

---

## 📊 Data Import into MySQL

```sql
LOAD DATA INFILE '/path/to/cleaned_sharktank.csv'
INTO TABLE sharktank
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
```

---

## 📊 Data Analysis (SQL Queries)

### 1. Top-Funded Industry

```sql
SELECT Industry, SUM(`Total_Deal_Amount(in_lakhs)`) AS Total_Industry_Investment
FROM sharktank
GROUP BY Industry
ORDER BY Total_Industry_Investment DESC
LIMIT 1;
```

### 2. Startups Without Investment Offers

```sql
SELECT Startup_Name, Industry
FROM sharktank
WHERE LOWER(Received_Offer) = 'no';
```

### 3. Startups with the Most Sharks in a Deal

```sql
SELECT Startup_Name, Number_of_Sharks_in_Deal
FROM sharktank
ORDER BY Number_of_Sharks_in_Deal DESC
LIMIT 5;
```

### 4. Identify Overvalued Startups

```sql
SELECT Startup_Name, `Valuation_Requested(in_lakhs)`, `Yearly_Revenue(in_lakhs)`
FROM sharktank
WHERE CAST(`Valuation_Requested(in_lakhs)` AS FLOAT) > 5 * CAST(`Yearly_Revenue(in_lakhs)` AS FLOAT)
ORDER BY `Valuation_Requested(in_lakhs)` DESC;
```

### 5. High Ask, Low Sales Startups

```sql
SELECT Startup_Name, Original_Ask_Amount, `Monthly_Sales(in_lakhs)`
FROM sharktank
WHERE CAST(`Monthly_Sales(in_lakhs)` AS FLOAT) < 1 AND Original_Ask_Amount > 50;
```

### 6. Gender Diversity in Presenters

```sql
SELECT SUM(Male_Presenters) AS TOTAL_MALE,
       SUM(Female_Presenters) AS TOTAL_FEMALE,
       SUM(Transgender_Presenters) AS TOTAL_TRANS
FROM sharktank;
```

### 7. Startup Origins by City

```sql
SELECT Pitchers_City, COUNT(*) AS Startup_Count
FROM sharktank
GROUP BY Pitchers_City
ORDER BY Startup_Count DESC
LIMIT 10;
```

### 8. Average Deal Size by Industry

```sql
SELECT Industry, AVG(`Total_Deal_Amount(in_lakhs)`) AS AVG_DEAL_AMT
FROM sharktank
WHERE LOWER(Accepted_Offer) = 'yes'
GROUP BY Industry
ORDER BY AVG_DEAL_AMT DESC;
```

### 9. Solo Founders

```sql
SELECT Startup_Name, Number_of_Presenters
FROM sharktank
WHERE Number_of_Presenters = 1;
```

### 10. Top-Funded Startup in Each Industry

```sql
SELECT * FROM (
  SELECT Industry, `Total_Deal_Amount(in_lakhs)`,
         ROW_NUMBER() OVER(PARTITION BY Industry ORDER BY `Total_Deal_Amount(in_lakhs)` DESC) AS rnk
  FROM sharktank
) a
WHERE rnk = 1;
```

### 11. Top 5 Industries by Total Funding

```sql
SELECT Industry, SUM(`Total_Deal_Amount(in_lakhs)`) AS Total_Funding
FROM sharktank
WHERE LOWER(Accepted_Offer) = 'yes'
GROUP BY Industry
ORDER BY Total_Funding DESC
LIMIT 5;
```

### 12. Top 5 Sharks by Total Investment

```sql
SELECT 'Namita' AS SHARK, SUM(`Namita_Investment_Amount(in lakhs)`) AS Total_Invested FROM sharktank
UNION
SELECT 'Vineeta', SUM(`Vineeta_Investment_Amount(in_lakhs)`) FROM sharktank
UNION
SELECT 'Anupam', SUM(`Anupam_Investment_Amount(in_lakhs)`) FROM sharktank
UNION
SELECT 'Aman', SUM(`Aman_Investment_Amount(in_lakhs)`) FROM sharktank
UNION
SELECT 'Peyush', SUM(`Peyush_Investment_Amount((in_lakhs)`) FROM sharktank
UNION
SELECT 'Amit', SUM(`Amit_Investment_Amount(in_lakhs)`) FROM sharktank
UNION
SELECT 'Ashneer', SUM(`Ashneer_Investment_Amount`) FROM sharktank
ORDER BY Total_Invested DESC
LIMIT 5;
```

---

## ✅ Conclusion

The Shark Tank data reveals a diverse startup ecosystem with pronounced trends in valuation, funding, and investor behavior. Startups in certain industries consistently secure higher funding, especially when backed by strong sales figures or larger teams. Investor decisions are influenced not only by metrics like revenue and profit but also by pitch quality and team composition.

Moreover, overvaluation and poor revenue traction appear to be major deterrents to investment. The findings emphasize the importance of financial health, realistic asks, and team diversity when seeking funding in high-stakes environments like Shark Tank.

---

## 📌 Key Takeaways

- **Realistic Valuation Matters:** Startups that requested funding well above their revenue base struggled to attract investors.
- **Team Size and Diversity Influence Investment:** Solo founders and lack of diversity did not correlate strongly with higher investments.
- **Consumer-Facing Sectors Dominate:** Industries like food, health, and tech attracted the highest deal amounts.
- **Investor Patterns Are Predictable:** A few sharks dominate funding activity, which can help future entrepreneurs tailor their pitches.
- **Cities Like Mumbai and Bangalore Lead:** Urban centers dominate startup pitches, highlighting regional entrepreneurial ecosystems.
