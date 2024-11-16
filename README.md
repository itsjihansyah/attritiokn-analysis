# Attrition Analysis
## Overview
Company X employs approximately 1300 employees this year. In the previous year, around 15% of these employees left the company, necessitating the replacement of theseemployees with new hires from the job market. This attrition rate is a concern for management due to several reasons:
* Project Delays: The departure of employees leads to delays in ongoing projects, making it challenging to meet deadlines and potentially harming the company's reputation among consumers and partners.
* Recruitment Costs: Maintaining a substantial recruitment department to continuously find and hire new talent incurs significant costs.
* Training Costs: New employees require training and time to acclimate to the company, leading to additional expenses and temporary reductions in productivity.

Given these challenges, it is crucial to understand and model employee attrition to mitigate its impact. The goal of this analysis is to use historical employee data to model attrition using XGBoost with PyCaret, and then apply this model to predict attrition for the current year's employees. The insights gained from this analysis will help HR management make informed decisions to reduce employee turnover and improve retention strategies.

## Dataset Description
The dataset used is from Kaggle. It contains 4410 entries (rows) and 31 columns (features). It consists of four datasets:
* General: Contains general data about Employees
* Manager Survey: Contains employees' feedback about their managers.
* Employee Survey: Contains survey results from employees.
* Time: Contains the duration of the employee’s working time.

## Business Questions
* What is the average job satisfaction level across different departments?
* How does the educational background of employees vary by department?
* What are the five highest employees’ working years?
* What is the distribution of employees by marital status?
* What is the overall predicted attrition rate of the company?
* Which department has the highest total attrition?
* How does job satisfaction impact attrition?
* What is the impact of marital status on attrition?
* How do the average years with current managers affect attrition?
* How do managerial performance ratings and job involvement levels relate to employee attrition?
Attrition mentioned in all business questions is the prediction score of attrition

## Data Relationship
In Power BI, we adopted a star schema approach to schema design. This involved organizing the dataset into a central fact table surrounded by multiple dimension tables, resembling the shape of a star.
* Fact Table:
  At the heart of the star schema lies the fact table, which contains quantitative metrics and transactional data. The "General" table is the primary fact table. The General tables changed to “FactGeneral”
* Dimension Tables:
  Dimension tables support the fact table, which provides descriptive context and additional attributes to enrich the analysis. These dimension tables cover the EmployeeSurvey, ManagerSurvey, and Time tables. Those table changed to “DimEmployeeSurvey”, “DimManagerSurvey”, and “DimTime”.
![Screenshot 2024-11-16 123826](https://github.com/user-attachments/assets/083d21a7-d472-4a6c-a638-24680322ab9d)

