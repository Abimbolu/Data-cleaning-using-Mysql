# Employee Analysis

## Table of Contents
- [Project Overview](#project-overview)
- [Data Source](#data-source)
- [Tools](#tools)
- [Data Cleaning/Preparation](#data-cleaning)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Results](#results)

  
### Project Overview

This data analysis project aims to provides insights about the employee's in the company. By cleaning and analyzing various aspects of this dataset, i seek to identify trends, make data driven recommendations and gain a depper understanding on the company and it's employees.

### Data Source
Employee data: The primary dataset used for this analysis is the 'employee.csv' file, containing detailed information about each employee in the company.

### Tools

- Excel - Data Preview
- SQL -Data Cleaning

### Data Cleaning
In the data preparation phases, i perfomed the following tasks;
- Data importing and Inspection
- Data Cleaning

### Exploratory Data Analysis 
EDA involves exploring the emp;oyee data to answer key questions such as;
- Top 5 highest average salary by job title
- Count of Gender in each business unit
- Top 5 Highest bonus rate by job title
- Annual Salary per Age bracket
- Total number of employees
- Total number of ehnic groups employed and their average salary
- Number of years each employees have stayed
- how many employees have resigned/sacked/retired


### Data Analysis

```Mysql
select * from employee;
-- To change hire date date_format to mysql standardized date format
UPDATE employee
	SET `hire date` = CASE
		WHEN `hire date` LIKE '%/%' THEN date_format(str_to_date(`hire date`, '%m/%d/%Y'), '%Y-%m-%d')
        WHEN `hire date` LIKE '%-%' THEN date_format(str_to_date(`hire date`, '%Y-%m-%d'), '%Y-%m-%d')
        ELSE NULL
        END;
        
-- To rename and change the data type of column hiredate
ALTER TABLE employee
CHANGE `hire date` hire_date DATE;

-- To change exit date date_format to mysql standardized date format
UPDATE employee
	SET `exit date` = CASE
		WHEN `exit date` LIKE '%/%' THEN date_format(str_to_date(`exit date`, '%m/%d/%Y'), '%Y-%m-%d')
        WHEN `exit date` LIKE ' ' THEN date_format(str_to_date(`exit date`, ' '), '%Y-%m-%d')
        END;

-- To replace the null/blank dates with '0000-00-00'
UPDATE employee
	SET `exit date` = COALESCE(`exit date`, '0000-00-00');

    UPDATE employee
	SET `exit date` = null
    WHERE `exit date` = '0000-00-00';
    
    UPDATE employee
	SET `exit date` = CURRENT_DATE()
    WHERE `exit date` IS NULL;
    
-- To rename exit date and change the data type
ALTER TABLE employee
CHANGE `exit date` exit_date DATE;

-- TO remove the dollar signs from annual salary
UPDATE employee
	SET `annual salary` = 
    REPLACE(REPLACE(`annual salary`, '$', ''), ',', '');
    
-- To rename and change the data type for annual salary
ALTER TABLE employee
CHANGE `Annual salary` annual_salary int;

-- To remove the % in Bonus column
UPDATE employee
	SET `Bonus %` =
    REPLACE(`Bonus %`, '%', '');
    
-- To change the name and data type of column bonus
ALTER TABLE employee
CHANGE `bonus` `Bonus_%` int;

-- To rename column business unit, job title and full name
ALTER TABLE employee
	CHANGE `business unit` business_unit text;

ALTER TABLE employee
	CHANGE `full name` Full_name text;
    
ALTER TABLE employee
	CHANGE `Job title` Job_title text;


-- ANALYSIS
-- 1) Top 5 highest average salary by job title
SELECT job_title, AVG(annual_salary) FROM employee 
	GROUP BY job_title ORDER BY AVG(annual_salary) DESC LIMIT 5;
    
-- 2) Count of Gender in each business unit
SELECT business_unit, gender, count(*) AS gender_count
	FROM employee
    GROUP BY business_unit, gender
    ORDER BY gender_count desc;
    
-- 3) Top 6 Highest bonus rate by job title
select AVG(`bonus_%`), job_title 
	FROM employee
    GROUP BY job_title
    order by AVG(`bonus_%`) DESC
    LIMIT 5;
    
-- 4) Annual Salary per Age bracket
Select 
	CASE WHEN age < 30 THEN 'Young'
		WHEN age >=30 AND age < 50 THEN 'Middle Age'
		WHEN age >= 50 THEN 'Old'
    ELSE 'OTHERS'
    END AS Age_bracket,
     count(*), AVG(Annual_salary)
    FROM employee
    group by Age_bracket
    Order by AVG(Annual_salary) desc;
    
-- 5) Total number of ehnic groups employed
SELECT ethnicity, count(*) AS ethnicity_count, AVG(annual_salary)
from employee
group by ethnicity
order by ethnicity_count, AVG(annual_salary) desc;

-- 6) Total number of employees
SELECT distinct COUNT(full_name) AS total_count from employee;

-- 7) Number of years each employees have stayed
SELECT *, ABS(TIMESTAMPDIFF(YEAR,exit_date, hire_date)) as employment_duration
	from employee order by employment_duration ;

-- 8)  how many employees have resigned/sacked/retired?
SELECT *,  CEIL(DATEDIFF(COALESCE(exit_date, curdate()), hire_date)/ 365) AS years_stayed 
	FROM employee
    WHERE exit_date <> '2023-11-09';
    ```

### Results
The analysis results are summarized as follows;
- Vice president, Director, Sr. Manager, Manager, Sr. Analyst are the top 5 highest average salary
- There are more females in three out of four business units
- Vice presidents, Director, Sr. Manager, Engineer Manager and Computer Systens Manager have the highest bonus rate
- Young (< 30 years) , middle age(>= 30 < 50) and old(> 50)
- There are 1000 employees
- Black, Latino, Caucasian and Asian accordingly
- 31 years is the maximum while the minimum is 2 months
- 85 employees have left






