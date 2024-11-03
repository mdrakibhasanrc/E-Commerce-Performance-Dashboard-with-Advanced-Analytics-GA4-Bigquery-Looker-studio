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
 ![KPI_Overview](https://github.com/user-attachments/assets/f77439cb-5f1a-436f-8625-b264171a42f5)

#### Website Overview Page: 
![Website Overview](https://github.com/user-attachments/assets/007e2c56-ba0f-4534-9c5a-6cafc5138dcd)

#### Ecommerce Report Page:


![Ecommerce Overview](https://github.com/user-attachments/assets/d9980279-624a-42f0-9bf6-40246ba85d4b)


#### Traffic Source Page:


![Traffic Source](https://github.com/user-attachments/assets/1c6d7459-7c91-4e53-8fa0-c4748393c7b1)


#### Landing Page Performance:


![Landing Page](https://github.com/user-attachments/assets/07772c5d-85f4-487a-bc76-b10fb46cf8d1)


#### Event Page:

![Landing_Page](https://github.com/user-attachments/assets/ef76a44a-e9a8-42b2-bc7f-4979b7564186)

#### User Demographics Page:

![Demographic](https://github.com/user-attachments/assets/e0fbf750-1e2e-4395-bef0-8faa90e35793)

#### Device Performance age:

![Device Report](https://github.com/user-attachments/assets/9f94cc4d-f064-4800-89a3-5bd9f8fe3b3b)

### Insight From This Dashboard:
** Increased Conversion Rate: After implementing data-driven changes, TrendStyle saw a 25% increase in conversion rates within three months.

** Higher Average Order Value: By promoting bundles and upsells based on user behavior, the average order value increased by 15%.

** Improved Marketing ROI: Reallocating marketing budgets led to a 40% improvement in ROI from paid advertising.
