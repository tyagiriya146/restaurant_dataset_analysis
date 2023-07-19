***
````sql
select * from consumers
select * from restaurant_cuisines
select * from restaurants
select * from ratings
select * from consumer_preferences
````
***

#### CHECK FOR DUPLICATES

````sql
select consumer_id,count(consumer_id) as Total_consumer
from consumers 
having(consumer_id)>1
````
- There's *no duplicate*
  
####  How many unique consumers are there in the table?

````sql
SELECT COUNT(distinct Consumer_ID) as Total_consumer
from consumers
````

#### What are the Total Customers in each state
````sql
SELECT
		 State,
		 count(consumer_id) as Total_Customers
FROM consumers
GROUP BY State
Order BY Total_Customers DESC
````
#### How many unique consumers are there  in each city?
````sql
SELECT 
		 city,
		 count(consumer_id) as Total_Customers
FROM 	consumers
GROUP BY city
Order BY Total_Customers DESC	
````
#### How many consumers are married and How many consumers are single ?

````sql
SELECT COUNT(*) AS TotalMarried FROM consumers WHERE Marital_Status = 'Married';

SELECT COUNT(*) AS Totalsingle FROM consumers WHERE Marital_Status = 'Single';
````
#### What is the min,max,average age of the consumers?

````sql
SELECT AVG(Age) AS AverageAge FROM consumers;
SELECT min(Age) AS AverageAge FROM consumers;
SELECT max(Age) AS AverageAge FROM consumers;
````

#### How many restaurants are listed in the dataset?

````sql
SELECT COUNT(Restaurant_ID) AS Total_restaurant
from restaurants
````
#### How many restaurants are listed in each state

````sql
SELECT state, COUNT(Restaurant_ID)  Total_restaurant
FROM restaurants
GROUP BY state
ORDER BY Total_restaurant desc
````
#### How many restaurants are listed in each city

````sql
SELECT city, COUNT(Restaurant_ID)  Total_restaurant
FROM restaurants
GROUP BY city
ORDER BY  Total_restaurant desc
````
#### Top 3 rated restaurants

````sql
select name,restaurant_id
from restaurants
where restaurant_id 
	IN (
	select restaurant_id
	from ratings
	where overall_rating = 2
	group by restaurant_id 
	order by count(restaurant_id) desc
    )
    limit 3

  ````  
#### Bottom 3 rated restaurants

````sql
select name,restaurant_id
from restaurants
where restaurant_id 
	IN (
	select restaurant_id
	from ratings
	where overall_rating = 0
	group by restaurant_id 
	order by count(restaurant_id) asc
    )
        limit 3
````
#### What are the parking options available at each restaurant?

````sql
SELECT 
		 parking,
		 count(restaurant_id) as Total_restaurant
FROM 	 restaurants
GROUP BY parking
Order BY Total_restaurant DESC
````
#### Transportation methods of customers
````sql
SELECT 
		 transportation_method,
		 count(consumer_id) as Total_Customers
FROM consumers
WHERE 	 transportation_method is not null	
GROUP BY transportation_method
Order BY Total_Customers DESC
````
#### What are the different price ranges of the restaurants and their corresponding counts
````sql
SELECT Price,
       COUNT(restaurant_id) AS restaurant_count
FROM   restaurants
GROUP BY Price
Order BY restaurant_count DESC
````
####  Budget level of customers
````sql
SELECT 
		 budget,
		 count(consumer_id) as Total_Customers
FROM 	 consumers
WHERE 	 budget is not null
GROUP BY budget
````
#### How many restaurants offer smoking allowed?
````sql
SELECT 
		 smoking_allowed,
		 count(restaurant_id) as Total_restaurant
FROM 	 restaurants
GROUP BY smoking_allowed
Order BY Total_restaurant DESC
````
#### Total Smokers by Occupation
````sql
SELECT 
		 occupation,
		 count(consumer_id) as Smokers
FROM 	 consumers
WHERE 	 smoker = 'Yes'
GROUP BY occupation
````
#### Drinking level of students
````sql
SELECT 
		 drink_level,
		 count(consumer_id) as student_count
FROM 	 consumers
WHERE 	 occupation = 'Student' and occupation is not null
GROUP BY drink_level
````
#### restaurants offer alcohol service?
````sql
SELECT 
		alcohol_service,
		 count(restaurant_id) as Total_restaurant
FROM 	 restaurants
GROUP BY alcohol_service
Order BY Total_restaurant DESC
````
#### How many unique cuisines are offered by the restaurants listed in the tables?
````sql
SELECT 
		cuisine,
		count(distinct restaurant_id) AS Total_restaurant
FROM 	restaurant_cuisines
GROUP BY cuisine
ORDER BY Total_restaurant DESC
````
#### Restaurant price analysis for each cuisine
````sql
SELECT
 b.cuisine,
SUM(CASE WHEN  a.Price ='High' Then 1 Else 0 END ) AS High,
SUM(CASE WHEN  a.Price ='Medium' Then 1 Else 0 END) AS Medium,
SUM(CASE WHEN  a.Price = 'Low' Then 1 Else 0 END) AS Low
FROM restaurants AS a
INNER JOIN restaurant_cuisines AS b
ON a.restaurant_ID = b.restaurant_ID
group by b.cuisine
ORDER BY b.cuisine
````

#### Finding out count of each cuisine in each state
````sql
SELECT
		b.cuisine,
		SUM(CASE WHEN a.state = 'Morelos' Then 1 Else 0 END) AS Morelos,
		SUM(CASE WHEN a.state = 'San Luis Potosi' Then 1 Else 0 END) AS San_Luis_Potosi,
		SUM(CASE WHEN a.state = 'Tamaulipas' Then 1 Else 0 END) AS Tamaulipas
FROM 	restaurants AS a
INNER JOIN restaurant_cuisines AS b
ON a.restaurant_id = b.restaurant_id
GROUP BY  b.cuisine
ORDER BY  b.cuisine
````
#### How many customers have preferences for each cuisine?
````sql
SELECT 
		consumer_id,
		count(preferred_cuisine) AS total_cuisines
FROM 	consumer_preferences
GROUP BY consumer_id
ORDER BY total_cuisines DESC
````
#### What are the top three most popular cuisines among customers?
````sql
SELECT Preferred_Cuisine ,count(Consumer_ID)  Customer_Count
FROM consumer_preferences
GROUP BY Preferred_Cuisine
ORDER BY Customer_Count DESC
LIMIT 3
````
#### What are the top three most preferred cuisines among customers for each city?

````sql
SELECT c.City, cp.Preferred_Cuisine, COUNT(DISTINCT cp.Consumer_ID) AS Customer_Count
FROM consumers c
LEFT JOIN consumer_preferences cp ON c.Consumer_ID = cp.Consumer_ID
GROUP BY c.City, cp.Preferred_Cuisine
HAVING Customer_Count > 0
ORDER BY c.City, Customer_Count DESC
````
#### Find the top 5 restaurants with the highest average overall rating:
````sql
SELECT Restaurant_ID, AVG(Overall_Rating) AS Average_Overall_Rating
FROM ratings
GROUP BY Restaurant_ID
ORDER BY Average_Overall_Rating DESC
LIMIT 5;
````
#### Calculate the average food rating for each cuisine:
````sql
SELECT rc.Cuisine, AVG(r.Food_Rating) AS Average_Food_Rating
FROM restaurant_cuisines rc
INNER JOIN ratings r ON rc.Restaurant_ID = r.Restaurant_ID
GROUP BY rc.Cuisine;
````
#### Average ratings of each restaurant including it's cuisine type
````sql
SELECT 
		a.name,
		ROUND(AVG(b.overall_rating),2)as overall_Rating,
		ROUND(AVG(b.food_rating),2)as food_rating,
		ROUND(AVG(b.service_rating),2)as service_rating,
		c.cuisine
FROM restaurants as a 
INNER JOIN ratings as b
ON a.restaurant_id = b.restaurant_id
INNER JOIN restaurant_cuisines AS c
ON a.restaurant_id = c.restaurant_id
GROUP BY a.name,c.cuisine
ORDER BY a.name
````
#### Total customers with highest ratings in all different criteria
````sql
SELECT 
		count(consumer_id) as total_customers
FROM 	ratings
WHERE 	overall_rating = 2 and
		food_rating = 2 and
		service_rating = 2
````
** To conduct **sentiment analysi** on the given ratings data,
##### we can create new columns that represent the sentiment of the reviews or comment
````sql
ALTER TABLE ratings ADD COLUMN overall_senti Varchar(50)
ALTER TABLE ratings ADD COLUMN food_senti Varchar(50)
ALTER TABLE ratings ADD COLUMN service_senti Varchar(50)

- we use the UPDATE statement to set the values of these columns based on the conditions provided

UPDATE ratings
SET overall_senti = CASE WHEN overall_rating = 0 then 'Negative'
					 	 WHEN overall_rating = 1 then 'Neutral'	
					 	 WHEN overall_rating = 2 then 'Positive'
					  	 END
WHERE overall_senti is null
)

  UPDATE ratings
SET food_senti = CASE 	 WHEN food_rating = 0 then 'Negative'
					 	 WHEN food_rating = 1 then 'Neutral'	
					 	 WHEN food_rating = 2 then 'Positive'
					  	 END
WHERE food_senti is null )

UPDATE ratings
SET service_senti = CASE WHEN service_rating = 0 then 'Negative'
					 	 WHEN service_rating = 1 then 'Neutral'	
					 	 WHEN service_rating = 2 then 'Positive'
					  	 END
WHERE service_senti is null
)

````
####  Analyzing the Sentiment of Customer Reviews:
````sql
CREATE VIEW overall AS 
SELECT 
		overall_senti,
		count(consumer_id) as total_customers
FROM 	ratings
GROUP BY overall_senti

CREATE VIEW food AS 
SELECT 
		food_senti,
		count(consumer_id) as total_customers
FROM 	ratings
GROUP BY food_senti

CREATE VIEW service AS 
SELECT 
		service_senti,
		count(consumer_id) as total_customers
FROM 	ratings
GROUP BY service_senti
````
````sql
SELECT
        a.overall_senti as Sentiment,
        a.total_customers as Overall_Rating,
        b.total_customers as food_Rating,
        c.total_customers as service_Rating
FROM overall as a
INNER JOIN food as b
ON a.overall_senti = b.food_senti
INNER JOIN service as c
ON a.overall_senti = c.service_senti
````
#### find a list of customers visiting local or outside restaurants 
````sql
SELECT 
          a.consumer_id as id,
          b.city as customer_city,
          c.name,
          c.city as restaurant_city,
          a.overall_senti,
          a.food_senti,
          a.service_senti,
CASE WHEN b.city = c.city THEN 'Local' ELSE 'Outside' END as Location_preference
FROM ratings as a
INNER JOIN consumers as b
ON a.consumer_id = b.consumer_id
INNER JOIN restaurants as c
ON a.restaurant_id = c.restaurant_id
  ````
#### Count of Customers Visiting Local and Outside Restaurants 
````sql
SELECT 
		Location_preference,
		count(*) as total_customers,
		count( distinct id) as distinct_customers 
        from (SELECT 
				a.consumer_id as id,
				b.city as customer_city,
				c.name,
				c.city as restaurant_city,
				a.overall_senti,
				a.food_senti,
				a.service_senti,
				CASE WHEN b.city = c.city THEN 'Local' ELSE 'Outside' END as Location_preference
			FROM ratings as a
			INNER JOIN consumers as b
			ON a.consumer_id = b.consumer_id
			INNER JOIN restaurants as c
			ON a.restaurant_id = c.restaurant_id) as cte
            GROUP BY 	Location_preference
````
#### Best restaurants for each cuisines by different ratings       
````sql
CREATE VIEW average_analysis as (
SELECT 
		a.name,
		ROUND(AVG(b.overall_rating),2)as overall_Rating,
		ROUND(AVG(b.food_rating),2)as food_rating,
		ROUND(AVG(b.service_rating),2)as service_rating,
		c.cuisine
FROM restaurants as a 
INNER JOIN ratings as b
ON a.restaurant_id = b.restaurant_id
INNER JOIN restaurant_cuisines AS c
ON a.restaurant_id = c.restaurant_id
GROUP BY a.name,c.cuisine
ORDER BY c.cuisine
)
````
####  Best cuisines by different ratings

 ````sql
SELECT 
First_value(cuisine) OVER(ORDER BY overall_rating desc) as overall,
First_value(cuisine) OVER(ORDER BY food_rating desc) as food,
First_value(cuisine) OVER(ORDER BY service_rating desc) as service
FROM average_analysis
LIMIT 1	
````
