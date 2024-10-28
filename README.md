# Exploratory Data Analysis MYSQL Project
## World Layoffs Data Analysis

**Project Overview**

This project focuses on cleaning, analyzing, and deriving insights from a dataset of global company layoffs. By exploring critical trends—such as total layoffs, layoff percentages, industry impacts, and temporal patterns—this project helps to identify the industries and regions most affected by layoffs, as well as the companies facing the highest job cuts. Utilizing SQL for data manipulation, the project demonstrates an extensive exploratory data analysis (EDA) on layoff trends across different metrics.

**Dataset** 

The analysis leverages a table, **layoffs_staging2**, which includes fields like:

* **company:** Name of the company
  
* **total_laid_off:** Number of employees laid off
  
* **percentage_laid_off:** Percentage of total employees laid off
  
* **funds_raised_millions:** Total funds raised by the company
  
* **industry:** Industry of the company
  
* **country:** Country where the layoffs occurred
  
* **date:** Date of the layoff
  
* **stage:** Growth stage of the company

**Analysis Objectives**

The project explores key questions to uncover trends and insights, including:

1) Maximum layoffs and layoff percentages across companies.
2) Companies with a 100% layoff rate.
3) Industry impact, identifying sectors with the highest layoffs.
4) Country-specific layoff trends.
5) Temporal patterns in layoffs by month and year.
6) Rolling total of layoffs to visualize layoffs over time.

**Analysis Steps**

Here is a breakdown of the SQL queries and analysis insights:

**1) Overview of Data:**

      SELECT * FROM world_layoffs.layoffs_staging2;

An initial review of the dataset structure and contents.

**2) Maximum Layoffs and Layoff Percentage:**

      SELECT MAX(total_laid_off), MAX(percentage_laid_off)
      FROM world_layoffs.layoffs_staging2;

Identifies the highest individual layoffs and layoff percentages.

**3) Companies with 100% Layoff Rate:**

      SELECT * FROM world_layoffs.layoffs_staging2
      WHERE percentage_laid_off = 1
      ORDER BY total_laid_off DESC;

Highlights companies with complete workforce layoffs, sorted by total layoffs.

**4) Top-Funded Companies with 100% Layoff Rate:**

      SELECT * FROM world_layoffs.layoffs_staging2
      WHERE percentage_laid_off = 1
      ORDER BY funds_raised_millions DESC;

Ranks 100% layoff companies by funds raised, showing the impact on heavily funded companies.

**5) Layoffs by Company:**

      SELECT company, SUM(total_laid_off)
      FROM world_layoffs.layoffs_staging2
      GROUP BY company
      ORDER BY 2 DESC;

Lists total layoffs per company, offering insights into the most impacted organizations.

**6) Date Range of Layoffs:**

      SELECT MIN(`date`), MAX(`date`)
      FROM world_layoffs.layoffs_staging2;

Determines the earliest and latest layoffs in the dataset.

**7) Industry Impact:**

     SELECT industry, SUM(total_laid_off)
     FROM world_layoffs.layoffs_staging2
     GROUP BY industry
     ORDER BY 2 DESC;

Analyzes which industries experienced the most layoffs.

**8) Country-Specific Layoff Analysis:**

      SELECT country, SUM(total_laid_off)
      FROM world_layoffs.layoffs_staging2
      GROUP BY country
      ORDER BY 2 DESC;

Displays total layoffs by country to assess geographical impact.

**9) Layoffs by Date:**

      SELECT `date`, SUM(total_laid_off)
      FROM world_layoffs.layoffs_staging2
      GROUP BY `date`
      ORDER BY 1 DESC;

Aggregates layoffs by date to visualize daily layoff trends.

**10) Yearly Layoff Totals:**

      SELECT YEAR(`date`), SUM(total_laid_off)
      FROM world_layoffs.layoffs_staging2
      GROUP BY YEAR(`date`)
      ORDER BY 1 DESC;

Summarizes yearly layoff totals to understand broader economic patterns.

**11) Layoffs by Company Stage:**

      SELECT stage, SUM(total_laid_off)
      FROM world_layoffs.layoffs_staging2
      GROUP BY stage
      ORDER BY 2 DESC;

Analyzes layoffs by company growth stage (e.g., Startup, Growth, Public).

**12) Monthly Layoffs Trend:**

      SELECT SUBSTRING(date,1,7) as `MONTH`, SUM(total_laid_off) 
      FROM layoffs_staging2
      WHERE SUBSTRING(date,1,7) IS NOT NULL
      GROUP BY `MONTH`
      ORDER BY 1 ASC;

Shows monthly layoff totals to observe layoffs over time.

**13) Rolling Total of Monthly Layoffs:**

      WITH Rolling_total AS
      (
      SELECT SUBSTRING(date,1,7) as `MONTH`, SUM(total_laid_off) AS total_off
      FROM layoffs_staging2
      WHERE SUBSTRING(date,1,7) IS NOT NULL
      GROUP BY `MONTH`
      ORDER BY 1 ASC
      )
      SELECT `MONTH`, total_off,
      SUM(total_off) OVER(ORDER BY `MONTH`) AS rolling_total
      FROM Rolling_total;

Utilizes a rolling total to see cumulative layoffs by month.

**14) Yearly Top 5 Companies by Layoffs:**

      WITH Company_Year (company, years, total_laid_off) AS
      (
      SELECT company, YEAR(`date`), SUM(total_laid_off)
      FROM world_layoffs.layoffs_staging2
      GROUP BY company, YEAR(`date`)
      ), 
      Company_Year_Rank AS
      (
      SELECT *,
      DENSE_RANK() OVER (PARTITION BY years ORDER BY total_laid_off DESC) AS Ranking
      FROM Company_Year
      WHERE years IS NOT NULL
      )
      SELECT * 
      FROM Company_Year_Rank
      WHERE Ranking <= 5;

Uses common table expressions (CTEs) to find the top 5 companies by layoffs per year.

**Conclusion**

This project provides insights into the layoff landscape across industries, countries, and companies. By analyzing and visualizing these patterns, we can better understand the economic and organizational challenges that lead to workforce reductions and gain perspective on how such events trend over time.






