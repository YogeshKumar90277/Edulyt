# --------------------------------------------Problem Statement Sanity Checks – Data Cleaning-----------------------------------------------------------------------
# ------------------------------------------Tasks---------------------------------------------------------------------------------------------------------------
# Monthly spend of each customer.
# Monthly repayment of each customer.
# Highest paying 10 customers.
# People in which segment are spending more money.
# Which age group is spending more money?
# Which is the most profitable segment?
# In which category the customers are spending more money?
# Impose an interest rate of 2.9% for each customer for any due amount.
# Monthly profit for the bank.



#   ----------------------------------------------------Answers----------------------------------------------------------------------------

                                                  

#                                                                         Task 1. Monthly Spend of Each Customer


SELECT Costomer, MONTH(Month) AS Month, SUM(Amount) AS TotalSpend
FROM Spend
GROUP BY Costomer, MONTH(Month);


Objective: Calculate the total monthly spend for each customer.

Columns Used:

Costomer: The unique identifier for each customer.

Month: The date of the transaction.

Amount: The transaction amount.

GROUP BY: We group by Customer and the month extracted from the Month column to calculate the total spend for each customer per month.

Importance: This helps in understanding the spending patterns of each customer on a monthly basis, which can be used to analyze seasonal trends and customer behavior.





#                                                                         Task 2. Monthly Repayment of Each Customer


SELECT Costomer, MONTH(Month) AS Month, SUM(Amount) AS TotalRepayment
FROM Repayment$
GROUP BY Costomer, MONTH(Month);


Objective: Calculate the total monthly repayment for each customer.

Columns Used:

Costomer: The unique identifier for each customer.

Month: The date of the repayment.

Amount: The repayment amount.

GROUP BY: We group by Costomer and the month extracted from the Month column to calculate the total repayment for each costomer per month.

Importance: Understanding repayment behavior is crucial for assessing credit risk and the financial health of costomers.



                                                         

#                                                                          Task3.Highest Paying 10 Customers

SELECT TOP 10 Costomer, SUM(Amount) AS TotalSpend
FROM Spend$
GROUP BY Costomer
ORDER BY TotalSpend DESC;



Objective: Identify the top 10 customers based on their total spend.

Columns Used:

Customer: The unique identifier for each customer.

Amount: The transaction amount.

GROUP BY: We group by Customer to calculate the total spend for each customer.

ORDER BY: We order the results in descending order of TotalSpend to get the top spenders.

Importance: Identifying high-value customers helps in tailoring personalized services and marketing efforts to retain them.





#                                                                           Task4. Segment Spending Analysis

SELECT ca.Segment, SUM(s.Amount) AS TotalSpend
FROM Spend$ s
JOIN ['Customer Acqusition$'] ca ON s.Costomer = ca.Customer
GROUP BY ca.Segment
ORDER BY TotalSpend DESC;



Objective: Calculate the total spending for each customer segment.

Columns Used:

Segment: The customer segment (e.g., Self Employed, Salaried).

Amount: The transaction amount.

JOIN: We join the Spend table with the CustomerAcquisition table on the Customer column to link spending data with customer segments.

GROUP BY: We group by Segment to calculate the total spend for each segment.

Importance: This helps in understanding which customer segments are the most lucrative and may benefit from targeted marketing campaigns.




#                                                                            Task5.Age Group Spending Analysis


SELECT 
    CASE 
        WHEN Age < 18 THEN 'Under 18'
        WHEN Age BETWEEN 18 AND 30 THEN '18-30'
        WHEN Age BETWEEN 31 AND 45 THEN '31-45'
        WHEN Age BETWEEN 46 AND 60 THEN '46-60'
        ELSE 'Above 60'
    END AS AgeGroup,
    SUM(s.Amount) AS TotalSpend
FROM Spend$ s
JOIN ['Customer Acqusition$'] ca ON s.Costomer = ca.Customer
GROUP BY 
    CASE 
        WHEN Age < 18 THEN 'Under 18'
        WHEN Age BETWEEN 18 AND 30 THEN '18-30'
        WHEN Age BETWEEN 31 AND 45 THEN '31-45'
        WHEN Age BETWEEN 46 AND 60 THEN '46-60'
        ELSE 'Above 60'
    END
ORDER BY TotalSpend DESC;




Objective: Calculate the total spending for each age group.

Columns Used:

Age: The age of the customer.

Amount: The transaction amount.

CASE Statement: The CASE statement categorizes customers into different age groups.

JOIN: We join the Spend table with the CustomerAcquisition table on the Customer column to link spending data with customer ages.

GROUP BY: We group by the CASE statement to calculate the total spend for each age group.

Importance: Understanding spending habits of different age groups helps in designing age-appropriate products and marketing strategies.





 #                                                                          Task6.Most Profitable Segment


SELECT ca.Segment, SUM(s.Amount - r.Amount) AS Profit
FROM Spend$ s
JOIN ['Customer Acqusition$'] ca ON s.Costomer = ca.Customer
LEFT JOIN Repayment$ r ON s.Costomer = r.Costomer AND MONTH(s.Month) = MONTH(r.Month)
GROUP BY ca.Segment
ORDER BY Profit DESC;


Objective: Calculate the profitability of each segment by subtracting repayments from spending.

Columns Used:

Segment: The customer segment.

Amount: The transaction and repayment amounts.

JOIN: We join the Spend and CustomerAcquisition tables to link spending with customer segments and then perform a left join with the 
Repayment table to include repayment data.

GROUP BY: We group by Segment to calculate the profit for each segment.

Importance: Identifying the most profitable segments helps in allocating resources and strategies to maximize returns.






  #                                                                        Task7.Category Spending Analysis


SELECT Type, SUM(Amount) AS TotalSpend
FROM Spend$
GROUP BY Type
ORDER BY TotalSpend DESC;



Objective: Calculate the total spending for each transaction category.

Columns Used:

Type: The type of transaction (e.g., JEWELLERY, AUTO).

Amount: The transaction amount.

GROUP BY: We group by Type to calculate the total spend for each category.

Importance: Understanding which categories are the most popular can help in creating promotions and discounts for those categories.




#                                                                         Task8.Interest Due Calculation



SELECT s.Costomer, 
       (SUM(s.Amount) - COALESCE(SUM(r.Amount), 0)) * 0.029 AS InterestDue
FROM Spend$ s
LEFT JOIN Repayment$ r ON s.Costomer = r.Costomer AND MONTH(s.Month) = MONTH(r.Month)
GROUP BY s.Costomer;



Objective: Calculate the interest due for each customer based on their outstanding amount.

Columns Used:

Customer: The unique identifier for each customer.

Amount: The transaction and repayment amounts.

JOIN: We perform a left join between the Spend and Repayment tables to include customers who have made repayments and those who haven't.

COALESCE: This function ensures that null values in repayments are treated as zero.

Interest Calculation: The difference between total spending and total repayments is multiplied by the interest rate (2.9%) to calculate 
the interest due.

Importance: This helps in calculating the additional revenue from interest on outstanding amounts.








 #                                                                    Task9.Monthly Profit for the Bank




SELECT MONTH(s.Month) AS Month, SUM(s.Amount - COALESCE(r.Amount, 0)) AS Profit
FROM Spend$ s
LEFT JOIN Repayment$ r ON s.Costomer = r.Costomer AND MONTH(s.Month) = MONTH(r.Month)
GROUP BY MONTH(s.Month);


Objective: Calculate the monthly profit for the bank by subtracting repayments from spending.

Columns Used:

Month: The date of the transaction.

Amount: The transaction and repayment amounts.

JOIN: We perform a left join between the Spend and Repayment tables to include customers who have made repayments and those who haven't.

GROUP BY: We group by the month to calculate the monthly profit.

Importance: Understanding monthly profit trends helps in financial planning and identifying periods of high or low profitability.


