# Pewlett-Hackard-Analysis

## Overview of Project 

### Purpose
The purpose of this analysis is to make the following tables in preparation for a "silver tsunami" at Pewlett-Hackard as many current employees reach retirement age.
- Determining the  number of retiring employees per title.
- Identifying employees who are eligible to participate in a mentorship program.


### Resources used
- Data Source : departments.csv, dept_emp.csv, dept_manager.csv, employees.csv, salaries.csv, titles.csv 
- Software : PostgreSQL 11.12, pgAdmin4 5.2

## Results
The csv files were imported into tables in PostgreSQL using pgAdmin. Information was then retrieved from the different tables, and then exported into csv files.

### Part 1: Determining the number of retiring employees per title.
To begin with, the employees (current or previous) reaching retirement age was determined by their birth dates being between the years 1952 and 1955.\
The following infomation was retrieved for them from the different tables, and exported as retirement_titles.csv.
- Employee number
- First name and last name
- Titles they have held at Pewlett-Hackard
- The range of dates when they have been employed at Pewlett-Hackard\
This was edited into another table that had only the most recent titles that the employees held or are currently holding (Figure showing part of the table is below), and was exported as unique_titles.csv

![unique_titles](https://user-images.githubusercontent.com/71800628/120910606-db58b900-c645-11eb-935f-d3292bbb105d.png)

A third table was created that had the count of the different titles of the current or previous employees that were identified as eligible for retirement, and was exported as retiring_titles.csv. 

![retiring_titles_original](https://user-images.githubusercontent.com/71800628/120910611-e6134e00-c645-11eb-9da0-7f14dd33bcc0.png)

### Part 2:Indentifying employees eligible for mentorship programs.
Current employees with birth dates in the year 1965 were considered. These would be senior employees (by age) who are not yet in the retirement age.\
A table was created to hold the following information,
- Employee number.
- First and last name.
- Birth date.
- Date when hired in the company.
- Date confirming the currently employeed status in the company.
- Title held at the current time.\
(Some of the employees may have had more than one title in the company but here only their current title is considered. Figure showing part of the table is below.)

![mentorship_eligibility](https://user-images.githubusercontent.com/71800628/120910621-f3c8d380-c645-11eb-915e-f0bcdd292242.png)

## Analysis
1. The total number of employees who will be eligible for retirement is 72,458.\
It is important to note that this number is lower than the number obtained in Part 1 of this analysis (90,398).\
In Part 1, the tables created did not filter the data to include only the current employees. There are employees who have held different titles at different periods of time in the same company, however there are few that are no longer working for the company currently. An example would be employee #10011 Mary Sluis (refer to table in Part 1 under Results section), who served in the role of staff from 1/22/1990 to 11/9/1996.\
Taking into account only those who are currently employed (refer to code below), the total number of employees who will be eligible for retirement is 72,458 (brought down from 90,398 as calculated incorrectly before).
```
SELECT COUNT (e.emp_no)
FROM employees AS e
	INNER JOIN dept_employees AS de
		ON (e.emp_no = de.emp_no)
WHERE (e.birth_date BETWEEN '1952-01-01' AND '1955-12-31')
	AND de.to_date = ('9999-01-01');
```

2. Around 70% of probable vacancies comprise of Senior Engineer, and Senior Staff. These are positions either of leadership, or that require specialized skills.\
(Please note that in this analysis, information of employees no longer employed at the company, such as employee #10011 Mary Sluis, has been included, and this will give an inaccurate assessment of the final count of titles that may become vacant. For eg. "staff" is overcounted by including Mary Sluis.)\
After adjusting for current employees only (see code below), the above percentage (70%) is accurately stated. The count of employees set to retire by titles is as follows.
```
SELECT COUNT (ti.title)
FROM employees AS e
	INNER JOIN titles AS ti
		ON (e.emp_no = ti.emp_no)
WHERE (e.birth_date BETWEEN '1952-01-01' AND '1955-12-31')
	AND ti.to_date = ('9999-01-01')
GROUP BY ti.title
ORDER BY COUNT(title) DESC;
```
![retiring_titles_accurate](https://user-images.githubusercontent.com/71800628/120910629-0216ef80-c646-11eb-9b70-bf867fd58510.png)

3. Positions that require mentorship or training will need to be indentified.\
Establishment of a knowledge transfer pathway that will be documented, and used to effectively communicate standard business practices to those who will fill in these positions, will aid in mentorship.

4. 1549 currently employeed people have been indentified as eligible for mentorship. Out of these around 71% are currently in a leadership role or are highly skilled (senior staff or senior engineer). 


## Summary 
1. "How many roles will need to be filled as the "silver tsunami" begins to make an impact?"\
Not every person who has the option to retire will retire.\
An analysis to see how many people are likely to work beyond their retirement age, specific to roles, and based on current trends in the company is reccomended. Adjustment to hiring prediction can then be done accordingly.

2. "Are there enough qualified, retirement-ready employees in the dapartments to mentor the next generation of Pewlett-Hackard employees?"\
From the above analysis, employees eligible for mentorship program has been obtained based upon their age (See figure above in Part 2 under Results section.)
An efficient and productive mentor-mentee ratio will depend upon the roles and the departments they serve.\
First the roles requiring mentorship will have to be defined. Then the number of eligible employees better suited to mentor these defined roles can be determined. It's prudent to base this choice upon the time they served in these roles rather than by their age.\
There may be employees in the age bracket determined in the present analysis, who may not have held their current title for a substantial period of time, and therefore may not be suitable to serve as a mentor for the role held by that title.\
Please note that the "from_date" in the table obtained here is the date when they were hired at Pewlett Hackard, and not of the time they began to serve in the role defined by their current title.

To address the above points, obtaining the following insights will be beneficial.
- A table of the employees, who are likely to take the option of retirement, along with the regular attrition levels, filtered by titles in different departments will give an accurate representation of predicted vacancies.

- A query returning employees who have served in the roles, defined for membership, for a required period of time.\
Furthermore if the query is sorted by different departments, it'll help to establish if there are sufficient mentors in those departments for the expected number of new hires.
