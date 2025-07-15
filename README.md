
# 💼 SQL Project: Global Layoffs Data Cleaning (2020–2023)

This project focuses on cleaning a dataset of global layoffs using MySQL, preparing it for analysis and visualization. The dataset was sourced from Kaggle - Layoffs 2022 Dataset, and the goal was to ensure data quality by addressing duplicates, nulls, formatting inconsistencies, and standardizing fields.

## 📊 Dataset Overview

The dataset contains information about layoffs across various companies, including details such as:

    Company name

    Industry

    Country

    Layoff date

    Number and percentage laid off

    Stage and funding

## Project Steps
### 1. Create a Staging Table

To protect raw data and perform transformations safely, a staging table was created:

CREATE TABLE layoffs_staging LIKE layoffs;
INSERT INTO layoffs_staging SELECT * FROM layoffs;

### 2. Remove Duplicates

We identified duplicate rows using ROW_NUMBER() and removed them using CTEs:

WITH DELETE_CTE AS (
  SELECT *, ROW_NUMBER() OVER (PARTITION BY ...) AS row_num
  FROM layoffs_staging
)
DELETE FROM layoffs_staging WHERE row_num > 1;

### 3. Standardize and Clean Data

    Replaced empty strings in the industry column with NULLs.

    Populated missing industries based on matching companies with existing industry values.

    Standardized variations like 'Crypto Currency' and 'CryptoCurrency' to 'Crypto'.

    Cleaned country names like 'United States.' → 'United States'.

    Converted the date field from text to DATE type.

### 4. Fix Data Types

UPDATE layoffs_staging SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');
ALTER TABLE layoffs_staging MODIFY COLUMN `date` DATE;

### 5. Handle Null Values

We chose to preserve null values in columns like total_laid_off and funds_raised_millions for accurate future analysis.
### 6. Remove Unusable Records

Records with both total_laid_off and percentage_laid_off missing were deleted as they lacked analytical value.

DELETE FROM layoffs_staging
WHERE total_laid_off IS NULL AND percentage_laid_off IS NULL;

### 7. Final Cleanup

Dropped temporary columns like row_num to tidy up the dataset.

ALTER TABLE layoffs_staging DROP COLUMN row_num;

## ✅ Outcome

The cleaned dataset is now ready for exploratory data analysis (EDA) and visualization. All critical inconsistencies and missing values have been addressed, ensuring a reliable foundation for further insights.
