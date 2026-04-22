Sprint 2 - Exploring Startup Trends With SQL Project

Task 1: Calculate the number of companies that have been closed down

SQL Query: 

SELECT COUNT(status)

FROM company

WHERE status = 'closed';

Task 2: Print the amount of money news-related companies from the USA have raised. Use data from the company table.

SQL Query:

SELECT funding_total

FROM company

WHERE category_code = 'news' AND country_code = 'USA'

ORDER BY funding_total DESC;

Task 3: Find the total amount of company acquisitions in US dollars. Select only the deals made in cash from 2011 to 2013, inclusive.

select SUM(price_amount)

from acquisition

WHERE term_code = 'cash' 

    AND (acquired_at >= '2011-01-01' AND acquired_at <= '2013-12-31');

Task 4: Print the first and last names of people whose Twitter usernames start with 'Silver'. Include their Twitter usernames in the results.

SELECT 
    twitter_username, first_name, last_name
    
FROM
    people
    
WHERE twitter_username
    LIKE 'Silver%';

Task 5: Print all information about people whose Twitter usernames include the word 'money' somewhere in their name and whose last names start with 'K'.

SELECT *

FROM people

WHERE twitter_username LIKE '%money%' AND last_name LIKE 'K%';

Task 6: For each country, calculate the total amount of money raised by companies registered there. The country code will tell you where each company is registered. Sort the data by sum in descending order to highlight the countries with the most funding.

SELECT 
    SUM(funding_total) AS total_amt_raised, country_code
    
FROM company

GROUP BY country_code

ORDER BY total_amt_raised DESC;

Task 7: Create a table showing the highest and lowest amount of money raised for each date in the funding_round table. Include the dates in your results. The resulting table should only have records where the lowest value is not equal to zero or to the highest value.

SELECT 
    funded_at,
    MAX(raised_amount),
    MIN(raised_amount)
    
FROM funding_round

GROUP BY funded_at

HAVING 
    MIN(raised_amount) <> MAX(raised_amount) AND MIN(raised_amount) <> 0;

Task 8: We need to categorize funds by their activity level to help our clients find appropriate partners. Create a field with three categories: high_activity — for funds that invest in a hundred or more companies. middle_activity — for funds that invest in between twenty (inclusive) to a hundred companies (exclusive).
low_activity — for funds that invest in fewer than twenty companies. Print all fields from the fund table and the new field with categories.

SELECT *,

    CASE 
    
        WHEN invested_companies >= 100 THEN 'high_activity'
        
        WHEN invested_companies >= 20 THEN 'middle_activity'
        
        ELSE 'low_activity'

    END AS activity
    
FROM fund;

Task 9: For each activity category you assigned in the previous task, calculate the average number of funding rounds the fund participated in. Round it to the nearest whole number. Print the categories and the average number of funding rounds. Sort the table by the average in ascending order.

SELECT 

    CASE 
    
        WHEN invested_companies >= 100 THEN 'high_activity'
        
        WHEN invested_companies >= 20 THEN 'middle_activity'
        
        ELSE 'low_activity'
        
    END AS activity,
    
    ROUND(AVG(investment_rounds)) AS average_rounds
    
FROM fund

GROUP BY activity

ORDER BY average_rounds;

Task 10: We'll start by identifying companies that closed after just one funding round, then analyze the educational backgrounds of their employees. First, make a list with the names of companies that closed down and had only one funding round while they existed. Then, find the employees who worked at these companies and join with the education table to analyze their degree types. Finally, calculate the average number of degrees per employee at these failed startups.

SELECT AVG(t.total_degree_type)

FROM (SELECT p.id,

      COUNT(e.degree_type) AS total_degree_type
      
      FROM people AS p JOIN education AS e ON p.id = e.person_id
      
      WHERE company_id IN (SELECT id
      
                           FROM company 
                           
                           WHERE id IN (SELECT company_id
                           
                                        FROM funding_round
                                        
                                        WHERE is_first_round = 1 AND is_last_round = 1)
                                        
                                        AND status = 'closed')
                                        
      GROUP BY p.id) AS t;




    
