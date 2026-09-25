# Recruitment dashboard

This repository stores a dashboard for recruitment process in Univio for Power BI Developer position

## Data sources

- Superstore dataset from Kaggle: https://www.kaggle.com/datasets/vivek468/superstore-dataset-final which was downloaded and uploaded to a VM as plain CSV file, becasue Kaggle allowed only to download the file zipped what would need Power Query to unpack it somehow.

- NBP currency rates API: https://api.nbp.pl pulled USD, assumed the values in the CSV dataset are in USD to calculate values in PLN; data pulled for the full period of the superstore dataset

- Time intelligence table **calendar** calculated in Power Query to handle all date relationships and calculations

## Data transformations

Superstore dataset has been normalized in terms of Product and Customer data to limit the number of duplicated data in Sales table. Regional (location) data has been left in the fact Sales table, because I assumed this data is connected to the specific order rather than the location of the customer as it differs even if orders are placed for one customer. The fact sales table had columns related to Customer and Product removed besides the IDs by which data is connected into star schema in the Power BI model.

Exchange rates from NBP api are pulled using a function to omit boilerplate code in Power Query. A function was needed, because the API only allows to pull data for max of 365 days and we need 3 years of exchange rates to cover the full superstore dataset range. Incremental refresh has been enabled for exchange rates, however the scope ends on 2017 so no new exchange rates will be pulled hence after initial caching the table should remain static and not infer any refresh time overhead.

## Calculations

Measures and calculations are described below.

### Calculated columns

fact_sales
- Cost - calculated by subtracting profit from revenue
- Sales PLN - Sales value multiplied by exchange rate for specific date based on OrderDate, if an exchange rate is not available for specific day (Sunday for example) then the last one is used

### Measures

- Revenue - sum of Sales
- Profit - sum of Profit
- Margin - profit divided by revenue
- Max Revenue - used to mark maximum value in chart with revenue
- Min Revenue - used to mark minimum value in chart with revenue
- Revenue PLN - sum of Sales PLN
- Revenue PY - revenue value for same period in previous year
- Revenue YOY% - percentage change of current revenue vs previous year
- Sales Volume - sum of Quantity from sales
- Total Cost - sum of Cost
- Total Sales All - measure ignoring the time filter context

### Parameters

There is a field parameter pie_switcher to showcase and allow the user to change what is displayed on the pie charts: Revenue, Profit, Margin etc.

