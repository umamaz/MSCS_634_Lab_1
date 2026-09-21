# MSCS_634_Lab_1
# MSCS 634 Lab 1: Data Visualization, Preprocessing, and Statistical Analysis

**Name:** Umama Azhar
**Course:** MSCS-634-M50, Advanced Big Data and Data Mining

## Purpose

This lab walks through a basic data analysis workflow in Python using Pandas and Matplotlib in a Jupyter Notebook. I loaded a supermarket sales dataset, explored it with visualizations, cleaned and reduced it, and then calculated descriptive statistics.

The four steps are:

1. **Data collection:** load the dataset into a DataFrame.
2. **Data visualization:** histogram and bar chart with written insights.
3. **Data preprocessing:** missing values, outliers, data reduction, scaling, and discretization.
4. **Statistical analysis:** overview, central tendency, dispersion, and correlation.

## Repository Contents

| File / Folder | Description |
|---|---|
| `DataLb1.ipynb` | The Jupyter Notebook with all code and outputs |
| `supermarket.csv` | The dataset used (9,800 orders, 18 columns) |
| `screenshots/` | All required screenshots for each step |
| `README.md` | This file |

## Dataset

The dataset is a Superstore-style retail sales file with 9,800 orders and 18 columns, including order and ship dates, customer segment, region, product category, and `Sales`. Orders run from 2015 to 2018.

The only real numeric measurement in the original file is `Sales`. To have a second numeric variable for the statistics and correlation steps, I created a `Shipping Days` column (Ship Date minus Order Date).

## Key Insights

### From the visualizations

- **Sales are strongly right-skewed.** The histogram shows about 8,500 of the 9,800 orders in the first bin, with a few very large orders stretching the tail past $20,000. This pointed to outliers before I had measured them.
- **Technology leads in total sales, but not because of order volume.** Technology had the highest total sales (about $827,000), ahead of Furniture (about $729,000) and Office Supplies (about $705,000). Office Supplies has the most orders (5,909) but the lowest average sale (about $119). Technology has the fewest orders (1,813) but the highest average sale (about $456).

### From the statistics (after outlier removal and 20% sampling, 1,731 rows)

- **Central tendency:** the mean Sales is about $92.88 but the median is only $40.74, so a few larger orders still pull the mean up. The most common Sales value is $12.96, though the mode is not very meaningful for dollar amounts. Shipping Days has a mean of 3.98, a median of 4, and a mode of 4, so shipping time is fairly symmetric.
- **Dispersion:** Sales range from $0.84 to $499.17 with a standard deviation of about $113.61 and an IQR of $112.76 (from $15.24 to $128.00). Shipping Days is tightly clustered, with an IQR of 2 days (3 to 5) and a standard deviation of about 1.77 days.
- **Correlation:** there is almost no linear relationship between any pair of numeric columns. Sales vs Shipping Days is about 0.006, so larger orders do not ship noticeably slower or faster. `Postal Code` is an identifier, not a measurement, so its correlations (about -0.005 and 0.005) are just noise.

## Preprocessing Decisions and Challenges

- **Dates stored as text.** `Order Date` and `Ship Date` loaded as strings in day-first format. I converted them with `pd.to_datetime(..., dayfirst=True)` before calculating `Shipping Days`.
- **Missing values.** Only `Postal Code` had missing values (11 rows), and all 11 belonged to Burlington, Vermont. I filled them with that city's ZIP code (05401) rather than dropping rows or using the mean, since an average makes no sense for an identifier. Because the column is numeric, it displays as `5401.0` and the leading zero is lost.
- **Outliers.** I used the IQR method on `Sales`: Q1 = 17.248, Q3 = 210.605, IQR = 193.357, giving an upper bound of about $500.64. The lower bound was negative, so no low outliers were possible. This flagged 1,145 orders (about 12%), which I removed into a new DataFrame, `df_no_outliers`, so the original stayed intact. These were real sales, not errors, so removing them loses some information, but it makes the remaining data easier to analyze. The maximum Sales dropped from $22,638.48 to $500.24.
- **Data reduction.** I took a random 20% sample (`random_state=42`), going from 8,655 rows to 1,731. I dropped four columns: `Row ID` (just a counter), `Customer Name` (duplicates `Customer ID`), `Product Name` (duplicates `Product ID`), and `Country` (always "United States", which I verified with `nunique()`). The result is 1,731 rows and 15 columns.
- **Scaling and discretization.** I applied Min-Max scaling, Z-score standardization, and decimal scaling (divide by 1,000) to `Sales`. For discretization I used hand-chosen dollar ranges instead of equal-width bins, because equal-width bins would put almost every order in "Low" on skewed data. The groups are Low (up to $50), Medium ($50 to $200), and High ($200 to $500), with 954, 501, and 276 orders respectively. The cutoffs are my own choice.
- **Correlation matrix scope.** I left the three scaled versions of `Sales` out of the correlation matrix because they are rescalings of the same values and would each show a correlation of 1 with `Sales`. With only two real measurements in the dataset, the weak correlations are a limit of the data. Also, a correlation near 0 means no *linear* relationship, not necessarily no relationship at all.

## How to Run

1. Put `supermarket.csv` in the same folder as the notebook.
2. Install the libraries if needed: `pip install pandas matplotlib`.
3. Open `DataLb1.ipynb` in Jupyter and choose **Kernel > Restart & Run All**.
