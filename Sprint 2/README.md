Sprint 2 - Exploring Startup Trends With SQL Project

Task 1: Calculate the number of companies that have been closed down
SQL Query: 
SELECT COUNT(status)
FROM company
WHERE status = 'closed';
