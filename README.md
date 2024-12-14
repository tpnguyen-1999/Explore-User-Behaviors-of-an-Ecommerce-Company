# SQL-Ecommerce-Analyzing
Using BigQuery to explore and analyze datasets from an e-commerce company.

## 1. Introduction and motivation
## 2. The goal of this project
## 3. Raw data

The E-commerce dataset is stored in the public Google Bigquery dataset. To access this dataset, follow these steps:
- Log in to your Google Cloud Platform account and create a new project.
- Navigate to the BigQuery console and select your newly created project.
- Select "Add Data" in the navigation panel and then "Search a project".
- Enter the project ID **"bigquery-public-data.google_analytics_sample.ga_sessions"** and click "Enter".
- Click on the **"ga_sessions_"** table to open it.

## 4. Explain dataset
https://support.google.com/analytics/answer/3437719?hl=en

|  Field Name | Data Type | Description |
| --- | --- | --- |
fullVisitorId                   |	STRING  |	  The unique visitor ID.|
date	                          | STRING  |   The date of the session in YYYYMMDD format.|
totals	                        | RECORD  |	  This section contains aggregate values across the session.|
totals.bounces	                | INTEGER |	  Total bounces (for convenience). For a bounced session, the value is 1, otherwise it is null.|
totals.hits	                    | INTEGER | 	Total number of hits within the session.|
totals.pageviews	              | INTEGER |	  Total number of pageviews within the session.|
totals.visits	                  | INTEGER |	  The number of sessions (for convenience). This value is 1 for sessions with interaction events. The value is null if there are no interaction events in the session.
totals.transactions	            | INTEGER	|   Total number of ecommerce transactions within the session.|
trafficSource.source	          | STRING	|   The source of the traffic source. Could be the name of the search engine, the referring hostname, or a value of the utm_source URL parameter.|
hits	                          | RECORD	|   This row and nested fields are populated for any and all types of hits.|
hits.eCommerceAction	          | RECORD	|   This section contains all of the ecommerce hits that occurred during the session. This is a repeated field and has an entry for each hit that was collected.|
hits.product	                  | RECORD	|   This row and nested fields will be populated for each hit that contains Enhanced Ecommerce PRODUCT data.|
hits.product.productQuantity	  | INTEGER |	  The quantity of the product purchased.|
hits.product.productRevenue	    | INTEGER |	  The revenue of the product, expressed as the value passed to Analytics multiplied by 10^6 (e.g., 2.40 would be given as 2400000).|
hits.product.productSKU	        | STRING  |	  Product SKU.|
hits.product.v2ProductName	    | STRING  |	  Product Name.|

## 5. Data Processing & Exploring
## 6. Ask & answer questions
**6.1: Calculate total visit, pageview, transaction for Jan, Feb and March 2017 (order by month)**
~~~sql
SELECT 
  FORMAT_DATE('%Y%m', PARSE_DATE('%Y%m%d', date)) month,
  SUM(totals.visits) visits,
  SUM(totals.pageviews) pageviews,
  SUM(totals.transactions) transactions
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*` 
WHERE _table_suffix BETWEEN '0101' AND '0331'
GROUP BY month
ORDER BY month;
~~~
|  month  |  visits  |  pageviews  |  transactions  |
|---------|----------|-------------|----------------|
|  201701  |  64694  |  257708  |  713  |
|  201702  |  62192  |  233373  |  733  |
|  201703  |  69931  |  259522  |  993  |

**6.2: Bounce rate per traffic source in July 2017**
~~~sql
SELECT 
  trafficSource.source source,
  SUM(totals.visits) total_visits,
  SUM(totals.bounces) total_no_of_bounces,
  ROUND(100.0*SUM(totals.bounces)/SUM(totals.visits),3) bounce_rate
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201707*` 
GROUP BY source
ORDER BY total_visits desc;
~~~
|  source  |  total_visits  |  total_no_of_bounces  |  bounce_rate  |
|---------|----------|-------------|----------------|

