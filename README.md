# Mentormind_Project
Automate the business loan approval system for a central bank using historical data of borrowers
#TASK: EXPLORE DDL AND DML COMMANDS

DDL:

CREATE:

CREATE TABLE sba_subset AS
SELECT *
FROM public."SBA_Madhuri"
WHERE "ApprovalFY" >= '2000' AND "ApprovalFY" <= '2005';

This will create a new table called "sba_subset" that contains all the columns and rows from the "SBA_Madhuri" table where the loan approval year is between 2000 and 2005.




ALTER:

ALTER TABLE public."SBA_Madhuri" ALTER COLUMN "DisbursementGross" TYPE numeric USING REGEXP_REPLACE("DisbursementGross"::text, '[^0-9.]', '', 'g')::numeric;
ALTER TABLE public."SBA_Madhuri" ALTER COLUMN "BalanceGross" TYPE numeric USING REGEXP_REPLACE("BalanceGross"::text, '[^0-9.]', '', 'g')::numeric;
ALTER TABLE public."SBA_Madhuri" ALTER COLUMN "ChgOffPrinGr" TYPE numeric USING REGEXP_REPLACE("ChgOffPrinGr"::text, '[^0-9.]', '', 'g')::numeric;
ALTER TABLE public."SBA_Madhuri" ALTER COLUMN "GrAppv" TYPE numeric USING REGEXP_REPLACE("GrAppv"::text, '[^0-9.]', '', 'g')::numeric;
ALTER TABLE public."SBA_Madhuri" ALTER COLUMN "SBA_Appv" TYPE numeric USING REGEXP_REPLACE("SBA_Appv"::text, '[^0-9.]', '', 'g')::numeric;

This command will alter the data type of several columns in the "SBA_Madhuri" table from text to numeric by using regular expressions to remove any non-numeric characters and converting the result to a numeric data type. It does so using multiple ALTER TABLE statements in a single line separated by semicolons.




DROP:
-- create small table
CREATE TABLE small_table AS
SELECT *
FROM public."SBA_Madhuri"small_table
LIMIT 100;

SELECT * FROM 

-- drop small table
DROP TABLE IF EXISTS small_table;

=========================================================================================================================================================================

DML COMMANDS:

Debt-to-Income Ratio (DTI):
DTI is the ratio of a borrower's monthly debt payments to their monthly income. A lower DTI indicates that the borrower has more disposable income available to repay the loan.

SELECT ("SUM(ChgOffPrinGr") + SUM("BalanceGross")) / SUM("DisbursementGross") AS DTI
FROM public."SBA_Madhuri"



Loan-to-Value Ratio (LTV):
LTV is the ratio of the loan amount to the appraised value of the collateral. A lower LTV indicates that the loan is less risky for the bank, as there is a lower chance of the collateral's value decreasing below the loan amount.

SELECT SUM("DisbursementGross") / SUM("AppraisedValue") AS LTV
FROM public."SBA_Madhuri"



Credit Score:
Credit score is a numerical value that reflects a borrower's creditworthiness. A higher credit score indicates that the borrower is more likely to repay the loan on time.

SELECT AVG("CreditScore") AS AvgCreditScore
FROM public."SBA_Madhuri"



Average loan amount (GrAppv) approved by the bank : 
This metric is important as it can help the bank in setting loan amounts that are reasonable and competitive.

SELECT ROUND(AVG("GrAppv"), 4) AS avg_loan_amount
FROM public."SBA_Madhuri";



Number of loans approved and declined by the bank:
This metric is important as it can help the bank understand its loan approval rate and identify areas for improvement.

SELECT "MIS_Status", COUNT(*) AS loan_count
FROM public."SBA_Madhuri"
GROUP BY "MIS_Status";



Average number of employees (NoEmp) for approved loans:
This metric is important as it can help the bank evaluate the borrower's ability to repay the loan based on the size of their business.

SELECT ROUND(AVG("NoEmp")::numeric, 4) AS avg_num_employees
FROM public."SBA_Madhuri"
WHERE "MIS_Status" = 'P I F';



The ratio of charged off principal amount (ChgOffPrinGr) to gross loan amount (GrAppv):
This metric is important as it can help the bank assess the creditworthiness of its borrowers and evaluate its loan portfolio risk.

SELECT ROUND(SUM("ChgOffPrinGr")/SUM("GrAppv"), 4) AS charge_off_ratio
FROM public."SBA_Madhuri";



Average loan term (Term) for approved loans:
This metric is important as it can help the bank set loan terms that are suitable for the borrower and minimize the risk of loan default.

SELECT ROUND(AVG("Term"), 4) AS avg_loan_term
FROM public."SBA_Madhuri"
WHERE "MIS_Status" = 'P I F';




UPDATE:
The query creates a subset table with 10 rows from "SBA_Madhuri," updates the name of a specific row, and selects that row from the subset table.

--Creating new subset
CREATE TABLE sba_subsets AS
SELECT *
FROM public."SBA_Madhuri"
LIMIT 10;

-- Updating  the "Name" column for a specific row in the "sba_subset" table
UPDATE sba_subsets SET "Name" = 'Jane Smith' WHERE "LoanNr_ChkDgt" = '8372434003';

SELECT * FROM sba_subsets WHERE "LoanNr_ChkDgt" = '8372434003'




INSERT:

The query creates a new table "sba_subset" with 5 rows and 5 columns by selecting limited data from "public."SBA_Madhuri" table, and displays the insert commands using the SELECT statement.

-- Creating a new table with 5 rows and 5 columns
CREATE TABLE sba_subset (
   LoanNr_ChkDgt VARCHAR(20),
   Name VARCHAR(255),
   City VARCHAR(255),
   State CHAR(2),
   Zip VARCHAR(10)
);

INSERT INTO sba_subset
SELECT "LoanNr_ChkDgt", "Name", "City", "State", "Zip"
FROM public."SBA_Madhuri"
LIMIT 5;

-- Displaying the insert command
SELECT 'INSERT INTO sba_subset (LoanNr_ChkDgt, Name, City, State, Zip) VALUES (''' || "LoanNr_ChkDgt" || ''', ''' || "Name" || ''', ''' || "City" || ''', ''' || "State" || ''', ''' || "Zip" || ''');'
FROM sba_subset;





EXPLORE JOINS: 
This will join the "SBA_Madhuri" table with the "sba_subset" table on the NAICS code, and then only select rows where the loan approval year is between 2000 and 2005. Finally, it will group the results by NAICS code and calculate the average loan amount for each group.

SELECT a."NAICS", AVG(b."GrAppv") AS avg_loan_amount
FROM public."SBA_Madhuri" a
JOIN sba_subset b ON a."NAICS" = b."NAICS"
WHERE a."ApprovalFY" >= '2000' AND a."ApprovalFY" <= '2005'
GROUP BY a."NAICS"
ORDER BY avg_loan_amount DESC;



JOIN:
This query creates a new table named "sba_subset" by selecting all columns from the "public."SBA_Madhuri" table where the loan approval year is between 2000 and 2005, then joins it with the "public."SBA_Madhuri" table based on the common NAICS column, selects the NAICS column and the average loan amount from the "sba_subset" table using the "AVG" function, and finally groups the result by the NAICS column and sorts it in descending order of average loan amount using the "GROUP BY" and "ORDER BY" statements respectively.

CREATE TABLE sba_subset AS
SELECT *
FROM public."SBA_Madhuri"
WHERE "ApprovalFY" >= '2000' AND "ApprovalFY" <= '2005';

SELECT * FROM sba_subset


SELECT a.NAICS, AVG(b.GrAppv) AS avg_loan_amount
FROM public."SBA_Madhuri" a
JOIN sba_subset b ON a.NAICS = b.NAICS
WHERE a."ApprovalFY" >= '2000' AND a."ApprovalFY" <= '2005'
GROUP BY a.NAICS
ORDER BY avg_loan_amount DESC;
==================================================================================================================================================================


TASK:

Build a review report on selected metrics
Report on why you chose the metrics you did and the thresholds (There is no right answer, Do some research about the general requirements of loan approval and feel free to play around with multiple metrics).


SELECT 
    "LoanNr_ChkDgt","MIS_Status","GrAppv","Term","NoEmp","DisbursementGross","ChgOffPrinGr",
    CASE
        WHEN "GrAppv" >= 50000 AND "GrAppv" <= 250000 AND "Term" <= 84 AND "NoEmp" <= 100 
		AND "DisbursementGross" <= 250000 AND "ChgOffPrinGr" <= 10000 THEN 'Approved'
        WHEN "GrAppv" >= 250000 AND "GrAppv" <= 500000 AND "Term" <= 120 AND "NoEmp" <= 250 
		AND "DisbursementGross" <= 500000 AND "ChgOffPrinGr" <= 50000 THEN 'Approved'
        ELSE 'Declined'
    END AS "Loan_Status"
FROM public."SBA_Madhuri";

This query selects several columns from the "SBA_Madhuri" table and uses a CASE statement to assign a loan status to each row based on certain criteria. The criteria for "Approved" loans are:

"GrAppv" (the approved loan amount) is between $50,000 and $250,000
"Term" (the loan term in months) is less than or equal to 84
"NoEmp" (the number of employees) is less than or equal to 100
"DisbursementGross" (the total amount disbursed) is less than or equal to $250,000
"ChgOffPrinGr" (the charged-off principal amount) is less than or equal to $10,000

The criteria for "Approved" loans with higher amounts are:

"GrAppv" is between $250,000 and $500,000
"Term" is less than or equal to 120
"NoEmp" is less than or equal to 250
"DisbursementGross" is less than or equal to $500,000
"ChgOffPrinGr" is less than or equal to $50,000
All other loans are assigned a status of "Declined".

The thresholds for these criteria and loan statuses were likely chosen based on industry standards, risk assessment, and the SBA's loan approval policies. The specific thresholds chosen may vary depending on the specific context and goals of the analysis.

=====================================================================================================================================================================================


Using Window functions, explore the top 3, 5 or 10 customers based on certain metrics (Eg - Find the top 5 customers with highest bank balance who have not defaulted on a loan in the last 2 years). This will help you understand your ideal loan applicants.

WITH CTE AS (
  SELECT "Name" ,"ApprovalDate","MIS_Status","GrAppv",
    row_number() over(PARTITION BY "MIS_Status"  ORDER BY "GrAppv" DESC) AS rowno
  FROM public."SBA_Madhuri"
)
SELECT "Name" ,"ApprovalDate","MIS_Status","GrAppv"
FROM CTE
WHERE "ApprovalDate" < TO_DATE('2012-01-01', 'YYYY-MM-DD') and "MIS_Status"='P I F' limit 5;


====================================================================================================================================================================================================



Write out your final query that creates a column in the table (Refer to the commands in C3) which says whether a customer is eligible for a loan or not based on the criterion you set in the previous component.

SELECT "Name", "MIS_Status",
  CASE
      WHEN "MIS_Status" ='CHGOFF' THEN 'Not eligible for loan'
      WHEN "MIS_Status" = 'P I F' THEN 'Eligible for loan'
      ELSE 'Not defined'
  END AS Loan_Eligibility
FROM public."SBA_Madhuri"
LIMIT 10000;













