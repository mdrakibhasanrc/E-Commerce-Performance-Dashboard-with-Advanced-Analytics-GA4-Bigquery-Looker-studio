### Problem Statment:
An e-commerce company, TrendStyle, specializes in selling fashion apparel and accessories online. Over the past year, they have experienced fluctuating sales, with a noticeable decline in conversion rates. To address these challenges, the company decided to leverage advanced analytics tools to gain deeper insights into customer behavior and optimize their marketing strategies.

### Objective:
The primary objective was to create a comprehensive dashboard using Looker Studio that integrates data from Google Analytics 4 (GA4), BigQuery. This would provide real-time insights into customer interactions, sales performance, and marketing effectiveness.

### Tech Stack: Ga4 | Bigquery, SQL | Looker Studio

### Implementation Steps:

✅ Integrated GA4 with BigQuery for enhanced data storage and analysis capabilities.
✅ Ensured that raw GA4 data was continuously streamed to BigQuery for real-time reporting.
✅ After Data Integration with Bigquery, I have been Custom sql query for to get deeper insights.
✅ Then Connect and integrate BigQuery on Looker Studio & Setup Looker Data Studio dashboard creation and development:

### SQL Query:
✅ Funnel Analysis to get Deeper Insights
```sql
with dataset as (
 SELECT 
 user_pseudo_id,
 event_name,
 parse_date('%Y%m%d',event_date) as event_date,
 timestamp_micros(event_timestamp) as event_timestamp
 FROM `bigquery-public-data.ga4_obfuscated_sample_ecommerce.events_*`
 where event_name in ('view_item','add_to_cart','begin_checkout','purchase')
),

view_item as (
 select
 user_pseudo_id,
 event_date,
 event_timestamp
 from dataset
 where event_name='view_item'
),

add_to_cart as (
 select
 user_pseudo_id,
 event_date,
 event_timestamp
 from dataset
 where event_name='add_to_cart'
),

begin_checkout as (
 select
 user_pseudo_id,
 event_date,
 event_timestamp
 from dataset
 where event_name='begin_checkout'
),

purchase as (
 select
 user_pseudo_id,
 event_date,
 event_timestamp
 from dataset
 where event_name='purchase'
),

funnel as (
 select
 vi.event_date,
 count(distinct vi.user_pseudo_id) as view_item,
 count(distinct atc.user_pseudo_id) as add_to_cart,
 count(distinct bc.user_pseudo_id) as begin_checkout,
 count(distinct p.user_pseudo_id) as purchase
 from view_item vi
 left join add_to_cart atc on vi.user_pseudo_id=atc.user_pseudo_id
 and vi.event_date=atc.event_date
 and vi.event_timestamp<atc.event_timestamp
 left join begin_checkout bc on atc.user_pseudo_id=bc.user_pseudo_id
 and atc.event_date=bc.event_date
 and atc.event_timestamp<bc.event_timestamp
 left join purchase p on bc.user_pseudo_id=p.user_pseudo_id
 and bc.event_date=p.event_date
 and bc.event_timestamp<p.event_timestamp
 group by vi.event_date
)

 select
 event_date,
 view_item,
 add_to_cart,
 begin_checkout,
 purchase,
 round(coalesce(add_to_cart/nullif(view_item,0),0),2) as add_to_cart_rate,
 round(coalesce(begin_checkout/nullif(view_item,0),0),2) as begin_checkout_rate,
 round(coalesce(purchase/nullif(view_item,0),0),2) as purchase_rate
 from funnel
 order by view_item desc
```


### Development Dashboard: 

#### ALL KPI Overview: 
 
![KPI ](https://github.com/user-attachments/assets/393a34c7-50fc-453a-b595-c1ed74135792)
![KPI Overview](https://github.com/user-attachments/assets/42916bde-f573-4a91-84cd-7f6d0d31d40f)

#### Ecommerce Overview:

![Ecommerce](https://github.com/user-attachments/assets/ec5459bc-9601-42b7-a76c-d00f3aa2f27e)


#### Event Overview

![Event](https://github.com/user-attachments/assets/bb121a99-5e8d-438c-905d-0df5df7c965f)


#### Top Performing Pages

![Landing_page](https://github.com/user-attachments/assets/78a2eb08-5e12-4b72-baad-8b6d7659c8b8)


#### Traffic Acquisition

![Traffic](https://github.com/user-attachments/assets/dd8bd49b-bab9-43e0-b4ca-183b7a5ded37)

#### User Demographics

![Demographics](https://github.com/user-attachments/assets/ddf89244-9a11-4d3f-b15b-8378c21a87de)

### Results
** Increased Conversion Rate: After implementing data-driven changes, TrendStyle saw a 25% increase in conversion rates within three months.

** Higher Average Order Value: By promoting bundles and upsells based on user behavior, the average order value increased by 15%.

** Improved Marketing ROI: Reallocating marketing budgets led to a 40% improvement in ROI from paid advertising.
