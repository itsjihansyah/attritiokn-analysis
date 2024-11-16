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

## DAX Measurement
In Power BI, creating measurements (measures) using DAX (Data Analysis Expressions) is crucial for deriving actionable insights and making data-driven decisions. Each measurement provides specific insights into different aspects of the data. Here are the measurements created along with the DAX code:
* Gender Ration
  Count Female Employees = CALCULATE(COUNT(FactGeneral[EmployeeID]), FactGeneral[Gender] = "Female")
  Count Male Employees = CALCULATE(COUNT(FactGeneral[EmployeeID]), FactGeneral[Gender] = "Male")

  Gender Ratio =
  VAR MaleCount = [Count Male Employees]
  VAR FemaleCount = [Count Female Employees]
  
  RETURN
  FORMAT(MaleCount, "0") & " : " & FORMAT(FemaleCount, "0")
  
* Mode of Job Satisfaction
  ModeJobSatisfaction =
  VAR SummaryTable =
      SUMMARIZE(
          'DimEmployeeSurvey',
          'DimEmployeeSurvey'[JobSatisfaction],
          "Count", COUNT('DimEmployeeSurvey'[JobSatisfaction])
      )
  VAR MaxCount =
      MAXX(
          SummaryTable,
          [Count]
      )
  VAR ModeJobSatisfactionValue =
      MAXX(
          FILTER(
              SummaryTable,
              [Count] = MaxCount
          ),
          'DimEmployeeSurvey'[JobSatisfaction]
      )
  RETURN
      ModeJobSatisfactionValue

* Mode of Performance Rating
  ModePerformanceRating =
  VAR SummaryTable =
      SUMMARIZE(
          'DimManagerSurvey',
          'DimManagerSurvey'[PerformanceRating],
          "Count", COUNT('DimManagerSurvey'[PerformanceRating])
      )
  VAR MaxCount =
      MAXX(
          SummaryTable,
          [Count]
      )
  VAR ModePerformanceRatingValue =
      MAXX(
          FILTER(
              SummaryTable,
              [Count] = MaxCount
          ),
          'DimManagerSurvey'[PerformanceRating]
      )
  RETURN
      ModePerformanceRatingValue

* Mode of Job Involvement
  ModeJobInvolvement =
  VAR SummaryTable =
      SUMMARIZE(
          'DimManagerSurvey',
          'DimManagerSurvey'[JobInvolvement],
          "Count", COUNT('DimManagerSurvey'[JobInvolvement])
      )
  VAR MaxCount =
      MAXX(
          SummaryTable,
          [Count]
      )
  VAR ModeJobInvolvementValue =
      MAXX(
          FILTER(
              SummaryTable,
              [Count] = MaxCount
          ),
          'DimManagerSurvey'[JobInvolvement]
      )
  RETURN
      ModeJobInvolvementValue

## Data Mining
Objective: Use machine learning techniques to predict employee attrition.
To implement this, we use PyCaret in Power BI using Python scripting. The steps include:
* Install Anaconda and necessary libraries, including Pycaret and XGboost. Ensure Python version 3.11.7 is used.
  conda create -n myenv311 python=3.11.7
  conda activate myenv311
  pip install pycaret
  pip install xgboost
* Load the dataset into the Power BI environment and perform the ETL process as previously outlined.
  Duplicate the “General” table and rename one of them to “General (model training)”.
* Insert the following Python script to the General (model training) to train the XGBoost model and get the model file:
  ### import classification module and setup environment
  from pycaret.classification import *
  clf1 = setup(dataset, target = ‘Attrition’)

  ### train and save xgboost model
  xgboost = create_model('xgboost', verbose = False)
  final_xgboost = finalize_model(xgboost)
  save_model(final_xgboost, 'C:/Users/JHsya/xgboost_attrition')
  
  ### Insert the following script in the General table to get the score prediction:
  from pycaret.classification import *
  xgboost = load_model('c:/users/JHsya/xgboost_attrition')
  dataset = predict_model(xgboost, data = dataset)

  ### Apply the previous script to the FactGeneral table to predict the attrition. Finally, the prediction will be visible on the FactGeneral Table.

