## Google Data Analytics Capstone:

Part of finishing the Google Data Analytics course is to choose and complete a capstone project. Upon completing the project, each student will be able to showcase their understanding of data analysis steps and technical skills that they have learned from the course. The project that I have chosen was the Cyclistic Cases Study.

Case Study Introduction:

Cyclistic is a fictional bike-share company that features more than 5800 bicycles and 600 docking stations across Chicago. It was launched in 2016 with various types of bikes- including ones with assistive features- can be unlocked from one station and returned to another. 

The company offers classic bikes and electric bikes, with some bikes includes assistive features that make bike-sharing more inclusive to people with disabilities. There are two customer groups: Casual and Annual Members. Casuals are customers who purchase a full day pass or single-ride pass. While Annual Members are customers that buy and annual membership. 

Ask:
 
The director of marketing, Lily Moreno, believes that the company should maximize the number of annual membership by converting the casual riders into annual members. As a member of the marketing analytics team, my assignment will be to analyze data to determine how member riders use Cyclistic bikes differently from casual riders? These insights will help guide the marketing strategy and provide the Cyclistic executive team with details and trends to approve the recommended marketing program. 

Prepare:

Cyclistic’s historical trip data is public data and was made available by licensed Motivate International Inc., and is separated monthly in CSV format. Each data-set contains every single trip that happened during that month. The datasets are appropriate and credible with no known biases. Some limitations with the data is that we wont be able to determine if casual riders live in the bike share service area, or if they have have purchased multiple single passes. This is due to data-privacy issues which won’t enable us to connect pass purchases to credit card numbers and prohibit us from using rider’s personal identifiable information.

For this project, I will be analyzing the data for the past 12 months, from January 2022 through December 2022. Due to the large data size of these files, I have downloaded and saved the data in a storage bucket on Google’s Cloud platform. 

Data Cleaning:

Since the datasets have large sizes, I have decided to use SQL on Big Query’s platform to process the data with the following steps:
1. I created a dataset called bicycle and then imported the 12 monthly CSV files separately from the storage bucket.
2. Combined all the 12 monthly tables with all their columns into one table called all_year_data to represent all bike trips for year 2022 using union all function. The combined table contained table contained a total of 5,667,717 rows and had the following 13 columns and schema with ride_id as primary key:




















3. Ran some queries to check what needs to be filtered and cleaned.
 A. Checked if all bike trips are unique and distinct with no duplicates using the distinct function on ride_id, as it is the primary key. This returned all ride_id variables are distinct and no further cleaning is necessary.  

B. Check how many types of bikes there are using the distinct function on rideable_type. This returned 3 types: 2601214 classic_bike, 2889029  electric_bike , and 177474  docked_bike. Docked bikes should be included in classic bikes. Therefor, we will rename docked_bikes with classic_bikes during the cleaning process.

C. Check if there are only two membership types: member and casual. Distinct function was used on member_casual variable and returned 3345685 member (annual members), and 2322032 casual members.

D. Check Length of bike trips and make sure they don’t exceed 24hrs and are not below 1 minute since those will probably outliers and biased. started_at and ended_at show the date and time of the start and end of each trip. I used timestamp function on started_at and ended_at variables to find total trip time, and where function to determine trips that are less than 1440 minutes (24hrs) and more than 1 minutes. This returned 5541269 rides and the rest will be filtered out during the cleaning process.

E. Since not all bike trips have start_station_name and end_station_name, I have opted to use the longitude (start_lng/end_lng) and latitude (start_lat/end_lat) of starting point and end point of the trip. where function was used to find which of these stations where null. There were 5858 trips with null end_lat, end_lng, start_lat, or start_lng. These will be also filtered out during the cleaning process. 

Cleaning the Data:
 I created a temporary table using the with function to be able to alternate the table without changing the variable permanently. The following alternations where done to the table:

    • docked_bike was replaced with classic_bike. So now we have only 2 types of bikes: electric_bike and classic_bike.
    • Extracted day of the week, month,  and hour the trip occurred. 
    • Calculated total bike trip time for every rider.
    • Cleaned out rows that had trips time more than 1440 minutes and less than 1min. 
    • Cleaned out rows that had trips with null start_lng, start_lat, end_lat, or end_lng. 
    • Calculated the total number of rides for each day of the week and each month.
    • Calculated average of ride length per day of the week. 
    • Calculated maximum ride length per day of the week. 

A total of 5540520 rows were left in the cleaned table and 127197 rows were removed. 
My data query can be viewed on Github here. 

Analyzing and sharing the data:

After cleaning and aggregating the data using SQL, an analysis was done to be able to answer the question “How do annual memebers and casual riders use Cyclistic bikes differently?” Tableau was used to create data visualizations of our analysis. 

First, I checked the bike type usage preferences between annual members and casual riders. 




















	Figure 1.




As we can see from Figure 1, the annual members accounted for 59% of the total bike trips in 2022, whereas casual riders made only 41% of the trips. Furthermore, there was almost equal preference for classic bike as for electric bikes in both membership types. 

Next, I plotted number of rides in each month during the year as shown in Figure 3. We can see that for annual members there is a steady usage of bikes during March through November (figure has wider bell shaped curve), and a usage decrease from December through February. This suggests that riders refrain from using the service during winter months due to impermissible weather conditions. On the other hand, casual riders use the bikes more between the months April-October with significant sharp increases during summer months. This suggests that casual riders might using it more for leisure, and annual members using it for commuting purposes.



To analyze further, I looked at number of rides taken by each membership types during the week ( Figure 2). From the graph we can see that annual members use the bikes more during the weekdays, and the number of trips decrease significantly on the weekends. This provides more evidence that annual members might be using the bikes for commuting purposes during business days. On the other hand, the figure shows casual riders use the bikes significantly more during the weekends than weekdays. Which might again indicate that casual riders use the bikes for leisure purposes.  












To further support this speculation, I analyzed the average trip length during the week. From Figure 4. we can see that casual riders in general use the bikes for a longer time when compared to annual members, with an average time of 21.69 minutes during the entire week; almost double the time of annual members that has a week average ride time of 12.17 minutes.  We can also see that average trip time for annual members stay steady during the week and increase slightly during the weekends. Whereas, casual riders the average trip time decreases during the weekdays and then increases significantly on the weekends.







Then I looked at bike usage during the day. From Figure 4, we can see for casual riders there is a steady increase in trips from 5 AM till the end of the day at 5 PM, then it starts to decrease through the rest of the day. This shows that casual bikers prefer to use the bikes more during midday hours. Whereas for annual members, the number of trips start to increase significantly from 5 AM, then showing three more evident time peaks at 8 AM, 12 PM, and at 5 PM, then is starts to decrease after 5PM. This is more evidence that annual members use it for business commuting purposes, where commuting times for work is typically at 8 AM and 5 PM, and 12 PM is lunch time. 



Then we looked at the map of Chicago and plotted top locations where riders started from in Figure 5. As we can see, there are some similar starting locations between casual and member riders due to probably using docking stations. However, we see that casual riders had significantly more starting locations near the water close to the pier, museums and aquarium. Whereas, member riders had more inland locations. 

 
















Similarly, for trip ending locations, we can see in map Figure that casual riders again preferred to visit areas closer to the water, pier, and attraction area. Which are all leisure and tourist attraction areas. However, annual member where ending their trips more inland closer to businesses. 
















In Summary, casual riders probably are more inclined to use bikes for leisure and tourism. They use the bikes more significantly during the weekend and summer months. They tend to start and end their trips close to tourist attraction areas in the city near the water. Also,  their average ride time is 2 times longer than members (21.69 min); suggesting that casual riders need the bikes longer during the day to get from one attraction to another. Whereas, annual members seem to use the bikes for commuting purposes. They use it year round with some decrease during winter months due to impermissible weather conditions. There is an increase in usage during the weekdays and during peak working commuting hours. Also they start and end their trips inland close to business locations. Also the average ride time is (12.17 min); half that of casual members. 
Acts/ Recommendations:
Based on my analysis after determining the key differences between casual riders and annual member riding patterns, the marketing department can start designing a new marketing strategy to convert casual riders into annual members with the following recommendations:
    • Since casual riders are most likely tourists, the team needs to create and offer different packages aimed at attracting this population. For example, offer a weekend or monthly memberships packages. 
    • Advertise during the summer months at tourist attraction locations.
    •  Offer incentives per mile usage for bikers. 

There were some limitations with the data, such as we weren’t able to cross check how many trips were done by each member and determine exact trip-time each member uses the bikes for. Also, obtain their original address or at least the zip-code of residence to determine where to focus advertisement and see if the company can expand services to interested population. 
