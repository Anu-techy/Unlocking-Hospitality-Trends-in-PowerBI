# Unlocking-Hospitality-Trends-in-PowerBI

**Description**

AtliQ Grands owns multiple five-star hotels across India. They have been in the hospitality industry for the past 20 years. Due to strategic moves from other competitors and ineffective decision-making in management, AtliQ Grands are losing its market share and revenue in the luxury/business hotels category. As a strategic move, the managing director of AtliQ Grands wanted to incorporate “Business and Data Intelligence” to regain their market share and revenue. However, they do not have an in-house data analytics team to provide them with these insights.

Their revenue management team had decided to hire a 3rd party service provider to provide them with insights from their historical data.

**Aim:**

To conduct a thorough data analysis of AtliQ Grands data and provide insights to the Revenue Team in the Hospitality Domain.

**Steps**

       1. Understanding problem statement and Business Model
       2. Data Collection and Understanding
       3. Data Cleaning and Exploration
       4. Data Transformation
       5. Insights Generation
       6. Recommendations
===========================================================================

**AtliQ Grand Business Model**

AtliQ Grand operates in four cities of India:  **Delhi, Mumbai, Bangalore and Hyderabad.**

They have the following types of properties under **Business and luxury category:**

       AtliQ Grands
       AtliQ Exotica
       AtliQ City
       AtliQ Blu
       AtliQ Bay
       AtliQ Palace
       AtliQ Seasons

These hotels have the following **room classes:**

       Standard
       Elite
       Premium
       Presidential

Hotel bookings can be done from various **booking platforms** like the company's website, 3rd party booking websites or direct offline.

**booking_status** are Checked Out, Cancelled, No Show

===========================================

**Data Collection and Understanding**

Data is extracted from Compay's Datawarehouse

**dim_date.csv**

Shape: (92,4)
Columns: date, mmmyy, weekno, day_type (weekday/weekend)

**dim_hotels.csv**

Shape: (25,4)
Columns: property_id, property_name, category, city

**dim_rooms.csv**

Shape: (4,2)
Columns: room_id, room_class

**fact_aggregated_bookings.csv**

Shape: (9200, 5) Columns: property_id, check_in_date, room_category, successful_bookings, capacity

**fact_bookings.csv**

Shape: (134590, 12) Columns: booking_id, property_id, booking_date, check_in_date, checkout_date, no_guests, room_category, booking_platform, ratings_given (1-5), booking_status, revenue_generated, revenue_realized

new_data_august.csv

Shape: (7, 13) Columns: property_id, property_name, category, city, room_category, room_class, check_in_date, mmm yy, week no, day_type, successful_bookings, capacity, occ%

The occupancy percentage (often called occ%) measures how many of the hotel's available rooms were actually sold and occupied.

           occ% =  (Rooms Sold / Total Rooms Available) * 100
===========================================


**Data Modelling**

Done modelling using **Star Schema** where the fact_tables are at the centre of the data model and the dimension tables are connected to them from sides.]

Below are all **one to many** relationships

       fact_bookins (property_id) --> dim_hotels (property_id)  
       fact_agg_bookings (property_id) --> dim_hotels (property_id)
       dim_date (date) --> fact_bookings (check_in_date)
       dim_date (date) --> fact_agg_bookings (check_in_date)
       dim_rooms (room_id) --> fact_bookings (room_category)
       dim_rooms (room_id) --> fact_agg_bookings (room_category)

**Data Cleaning/Transformations**

According to the Client/Subject matter Expert Fridays and Saturdays are considered **Weekend** and Sunday to Thursday is considered **weekday**. 
But in dim_date, after verification we realized the weekends are saturday and sunday. So I removed day_type column in dim_date.
and will create new day_type column according to the business logic.

**Creating Calculated Columns using DAX:**

Created a new column week number (WN) in dim_date table as:
WN = WEEKNUM(dim_date[date])

According to the Client/Subject matter Expert Fridays and Saturdays are considered **Weekend** and Sunday to Thursday is considered **weekday**. 
But in dim_date, after verification we realized the weekends are satuday and sunday. So I removed day_type column in dim_date.
 
To create new day_type column according to the business logic.

day_type = WEEKDAY(dim_date[date])

this will give numbers like sunday -1, monday -2 and so on saturday - 7

Modified the above formula as per our requirement.

day_type = IF(WEEKDAY(dim_date[date])>5,"Weekend","Weekday")

**Creating Measures using DAX:**

Attached the measures file





12 records out of 134578 records have -ve no of guests, have been removed

df_bookings["revenue_generated"].max() is in millions clearly suggesting presence of outliers in that column. Hence filtered 5 outliers whose values > mean + 3 * std

Identified outliers in df_bookings["revenue_realized"] as per statistical method but didnot remove them as per the context. Thorough analysis of this is attached in the file

checked for null values using df_bookings.isnull().sum() and ratings_given has 77897 null values. People generally give review /ratings all the time, hence na handling not done for this column.

2 null values in capacity column, replaced with median

Generally, the number of successful bookings <= room's capacity.But there are 6 records of that sort. If you find rows where successful_bookings exceeds the capacity, it could indicate Data Quality Issue. Hence filtered those rows.

===================================================

Data Transformation

Created a occ% column in df_agg_bookings

     occ% = (successful_booking/capacity) * 100
====================

Insights Generation

occ% as per room_class
Average occupancy rate per city
Weekday vs Weekend Performance
Occupancy in different cities in different months
Revenue realized per city
monthly revenue realized
Revenue realized per hotel type
Average rating per city
Revenue realized per booking platform
===============================

Recommendations

Consider incentives for customers to book less popular room classes, such as discounts or value-added services.

For cities with lower occupancy rates, focus on local partnerships with tourism boards or businesses to encourage more visitors. launch targeted marketing campaigns or promotions in those cities to boost demand.

Weekend occupancy is 30% more than weekdays occupancy. Offering discounts for weekday stays, Bundling services (e.g., free breakfast or spa vouchers) to entice weekday bookings.

Consider dynamic pricing for weekends,peak seasons to increase revenue.

Plan seasonal campaigns or events that attract tourists during off-peak months. For example, offer special deals or add experiences like local events or festivals.

Consider expanding hotel capacity or improving services to increase revenue even further. You can also focus on premium room offerings or luxury upgrades. Use the high revenue cities to cross-promote and offer deals to customers planning to visit cities with lower revenues.

Review and optimize pricing strategies for low-revenue months to make them more competitive (e.g., offering deals or discounts).

If specific hotel types aren't performing well or with lower ratings, review the amenities and services offered to make sure they meet customer expectations. Timely quality control checks should be performed

Diversify platform usage: If revenue heavily depends on a single platform, consider expanding your presence on multiple platforms to diversify your revenue streams and reduce dependency on any one source. Offer exclusive deals or early bird discounts on platforms that underperform to encourage more bookings.
