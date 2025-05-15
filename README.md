# Uncovering the Hidden Links: Depression, Lifestyle, and Socioeconomic Realities in a Public Health Dataset Using SQL

### Project Overview
Depression is a pervasive mental health condition that affects millions of individuals worldwide, with significant implications for both physical health and quality of life. According to the World Health Organization (WHO), depression is among the leading causes of disability globally, with its roots often intertwined with socioeconomic disparities, lifestyle behaviors, and chronic medical conditions.

This project leverages SQL to analyze a synthetic public health dataset focused on understanding the multifaceted nature of depression. Through detailed exploration of lifestyle behaviors (such as smoking, alcohol consumption, and physical activity) and socioeconomic factors (like income, education, and employment status), the analysis uncovers hidden patterns that contribute to mental health challenges. Advanced SQL querying techniques, including joins, aggregations, conditional logic, and Common Table Expressions (CTEs), are employed to dissect these complex relationships, providing insights that can inform targeted public health interventions.

### Data Source 
A synthetic public health dataset sourced from [Kaggle](https://www.kaggle.com/datasets/anthonytherrien/depression-dataset/data), designed to simulate realistic population-level health and behavioral patterns.

### Tools
SQL (Structured Query Language) for querying and analysis.

### Data Cleaning
The dataset was checked for null values, but none were found. The Name column was deleted as it was not relevant to the analysis, and the Gender column was added to enhance demographic segmentation and analysis.

### Data Structure
Three main tables were created to represent the dataset:

` Individuals ` - Captures demographic and socioeconomic information, including age, gender, marital status, education, employment, and income levels.

` HealthProfile ` - Represents lifestyle behaviors and chronic medical conditions, including smoking status, alcohol consumption, dietary habits, and chronic illnesses.

` LifestyleHistory ` - Details mental health history, substance use, physical activity levels, and family history of depression.

### Exploratory Data Analysis (EDA)
EDA invloved asking the the following questions.
- What is the prevalence of chronic medical conditions among different age groups, employment status, education level and marital statuses? 
- How does employment status correlate with smoking, alcohol consumption, and dietary habits? 
-	Are there noticeable patterns in lifestyle history (e.g., physical activity levels, substance use) across different income brackets?
- Do individuals with higher education levels have lower rates of smoking and alcohol consumption?
- How does unhealthy dietary habit vary with education levels?
- Do individuals with a family history of depression report more chronic medical conditions?
- How does physical activity influence mental health outcomes among individuals with a family history of depression?
- Are those with a family history of depression more likely to report poor dietary habits and low physical activity?
- Can individuals be clustered into risk categories based on lifestyle and health indicators?
- Which demographic factors (age, income, education) are the strongest predictors of high-risk mental health outcomes?

### Queries 
```sql
CREATE DATABASE CausesofDepression;

--creating individuals table 
CREATE TABLE Individuals (
    IndividualID VARCHAR(10) PRIMARY KEY,
    Name VARCHAR(255),
    Age INT,
    MaritalStatus VARCHAR(20),
    EducationLevel VARCHAR(50),
    NumberOfChildren INT,
    EmploymentStatus VARCHAR(20),
    Income DECIMAL(15, 2),
    Gender VARCHAR(10)
);

--Deleting name column
ALTER TABLE Individuals
DROP COLUMN Name;

--Importing my data 
BULK INSERT Individuals
FROM 'C:\Users\OLUWASEYI\Downloads\depression dataset\individual.csv'
WITH (
    FIELDTERMINATOR = ',',
    ROWTERMINATOR = '\n',
    FIRSTROW = 2
);

--creating Health Profile table
CREATE TABLE HealthProfile (
    HealthProfileID VARCHAR(10) PRIMARY KEY,
    IndividualID VARCHAR(10),
    SmokingStatus VARCHAR(20),
    AlcoholConsumption VARCHAR(20),
    DietaryHabits VARCHAR(20),
    SleepPatterns VARCHAR(20),
    ChronicMedicalConditions VARCHAR(5),
    FOREIGN KEY (IndividualID) REFERENCES Individuals(IndividualID)
);
--Importing my data 
BULK INSERT HealthProfile
FROM 'C:\Users\OLUWASEYI\Downloads\depression dataset\HealthProfile.csv'
WITH (
    FIELDTERMINATOR = ',',
    ROWTERMINATOR = '\n',
    FIRSTROW = 2
);
--Creating Lifestyle History Table
CREATE TABLE LifestyleHistory (
    LifestyleHistoryID VARCHAR(10) PRIMARY KEY,
    IndividualID VARCHAR(10),
    PhysicalActivityLevel VARCHAR(20),
    HistoryOfMentalIllness VARCHAR(5),
    HistoryOfSubstanceAbuse VARCHAR(5),
    FamilyHistoryOfDepression VARCHAR(5),
    FOREIGN KEY (IndividualID) REFERENCES Individuals(IndividualID)
);

--Importing my data 
BULK INSERT LifestyleHistory 
FROM 'C:\Users\OLUWASEYI\Downloads\depression dataset\LifestyleHistory.csv'
WITH (
    FIELDTERMINATOR = ',',
    ROWTERMINATOR = '\n',
    FIRSTROW = 2
);

--Social Demographic Distribution of the Population 

--1. Age 

SELECT 
  CASE 
    WHEN Age BETWEEN 18 AND 27 THEN '18-27'
    WHEN Age BETWEEN 28 AND 37 THEN '28-37'
    WHEN Age BETWEEN 38 AND 47 THEN '38-47'
    WHEN Age BETWEEN 48 AND 57 THEN '48-57'
    WHEN Age BETWEEN 58 AND 67 THEN '58-67'
    WHEN Age BETWEEN 68 AND 80 THEN '68-80'
    ELSE '80+'
  END AS AgeGroup,
  COUNT(*) AS Total,
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals
GROUP BY 
  CASE 
    WHEN Age BETWEEN 18 AND 27 THEN '18-27'
    WHEN Age BETWEEN 28 AND 37 THEN '28-37'
    WHEN Age BETWEEN 38 AND 47 THEN '38-47'
    WHEN Age BETWEEN 48 AND 57 THEN '48-57'
    WHEN Age BETWEEN 58 AND 67 THEN '58-67'
    WHEN Age BETWEEN 68 AND 80 THEN '68-80'
    ELSE '80+'
  END
  ORDER BY Percentage;

--2. Gender
  SELECT 
  Gender,
  COUNT(*) AS Total,
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals
GROUP BY Gender
ORDER BY Percentage;
 
--3. Marital Status 

SELECT 
  MaritalStatus,
  COUNT(*) AS Total,
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals
GROUP BY MaritalStatus
ORDER BY Percentage;
 
--4. Education Level 
SELECT 
  EducationLevel,
  COUNT(*) AS Total,
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals
GROUP BY EducationLevel
ORDER BY Percentage;
 
--5. Employment Status 

SELECT 
  EmploymentStatus,
  COUNT(*) AS Total,
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals
GROUP BY EmploymentStatus
ORDER BY Percentage;

 
--6. Number of Children 
SELECT 
  NumberOfChildren,
  COUNT(*) AS Total,
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals
GROUP BY NumberOfChildren;
 
--7. Income 

 SELECT 
  CASE 
	 WHEN Income BETWEEN 0 AND 10000 THEN '0-10,000'
     WHEN Income BETWEEN 10001 AND 60000 THEN '11,000-60,000'
     WHEN Income BETWEEN 60001 AND 110000 THEN '61,000-110,000'
     WHEN Income BETWEEN 110001 AND 160000 THEN '111,000-160,000'
     WHEN Income BETWEEN 160001 AND 210000 THEN '161,000-210,000'
     ELSE '210,000+'
  END AS IncomeBracket,
 COUNT(*) AS Total,
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals
GROUP BY 
  CASE 
	  WHEN Income BETWEEN 0 AND 10000 THEN '0-10,000'
      WHEN Income BETWEEN 10001 AND 60000 THEN '11,000-60,000'
      WHEN Income BETWEEN 60001 AND 110000 THEN '61,000-110,000'
      WHEN Income BETWEEN 110001 AND 160000 THEN '111,000-160,000'
      WHEN Income BETWEEN 160001 AND 210000 THEN '161,000-210,000'
      ELSE '210,000+'
  END
  ORDER BY Percentage;
 
-- Lifestyle & Health Factor Distributions
  --1. Smoking Status
 
 SELECT SmokingStatus, 
 COUNT(*) AS Total,
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM HealthProfile) AS DECIMAL(5,2)), 2) AS Percentage 
FROM HealthProfile
GROUP BY SmokingStatus
ORDER BY Percentage;

--2. Alcohol Consumption

SELECT AlcoholConsumption, 
 COUNT(*) AS Total,
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM HealthProfile) AS DECIMAL(5,2)), 2) AS Percentage 
FROM HealthProfile
GROUP BY AlcoholConsumption
ORDER BY Percentage;

--3. Dietary Habits
SELECT DietaryHabits, 
 COUNT(*) AS Total,
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM HealthProfile) AS DECIMAL(5,2)), 2) AS Percentage 
FROM HealthProfile
GROUP BY DietaryHabits
ORDER BY Percentage;

--4. Sleep patterns
SELECT SleepPatterns, 
 COUNT(*) AS Total,
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM HealthProfile) AS DECIMAL(5,2)), 2) AS Percentage 
FROM HealthProfile
GROUP BY SleepPatterns
ORDER BY Percentage;
--5. presence of chronic medical condition

SELECT ChronicMedicalConditions, 
 COUNT(*) AS Total,
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM HealthProfile) AS DECIMAL(5,2)), 2) AS Percentage 
FROM HealthProfile
GROUP BY ChronicMedicalConditions
ORDER BY Percentage;

--6. Physical Activity Level 

SELECT PhysicalActivityLevel, 
 COUNT(*) AS Total,
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM HealthProfile) AS DECIMAL(5,2)), 2) AS Percentage 
FROM LifestyleHistory
GROUP BY PhysicalActivityLevel
ORDER BY Percentage;

--7. History of Mental Illness

SELECT HistoryOfMentalIllness, 
 COUNT(*) AS Total,
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM HealthProfile) AS DECIMAL(5,2)), 2) AS Percentage 
FROM LifestyleHistory
GROUP BY HistoryOfMentalIllness
ORDER BY Percentage;

--8. History of Substance Abuse
SELECT HistoryOfSubstanceAbuse, 
 COUNT(*) AS Total,
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM HealthProfile) AS DECIMAL(5,2)), 2) AS Percentage 
FROM LifestyleHistory
GROUP BY HistoryOfSubstanceAbuse
ORDER BY Percentage;

--9. Family History of depression

SELECT FamilyHistoryOfDepression, 
 COUNT(*) AS Total,
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM HealthProfile) AS DECIMAL(5,2)), 2) AS Percentage 
FROM LifestyleHistory
GROUP BY FamilyHistoryOfDepression
ORDER BY Percentage;

/*Q1. What is the prevalence of chronic medical conditions among different age groups, 
employment status, education level and marital statuses?*/

--prevalence of chronic medical conditions among different age groups 
SELECT
 CASE 
        WHEN Age BETWEEN 18 AND 27 THEN '18-27'
        WHEN Age BETWEEN 28 AND 37 THEN '28-37'
        WHEN Age BETWEEN 38 AND 47 THEN '38-47'
        WHEN Age BETWEEN 48 AND 57 THEN '48-57'
        WHEN Age BETWEEN 58 AND 67 THEN '58-67'
        WHEN Age BETWEEN 68 AND 80 THEN '68-80'
        ELSE '80+' 
    END AS AgeGroup,
  h.ChronicMedicalConditions, COUNT(*) AS Total, 
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals i
JOIN HealthProfile h ON i.IndividualID = h.IndividualID
WHERE H.ChronicMedicalConditions = 'Yes'
GROUP BY  CASE 
        WHEN Age BETWEEN 18 AND 27 THEN '18-27'
        WHEN Age BETWEEN 28 AND 37 THEN '28-37'
        WHEN Age BETWEEN 38 AND 47 THEN '38-47'
        WHEN Age BETWEEN 48 AND 57 THEN '48-57'
        WHEN Age BETWEEN 58 AND 67 THEN '58-67'
        WHEN Age BETWEEN 68 AND 80 THEN '68-80'
        ELSE '80+' 
    END ,
	h.ChronicMedicalConditions
	ORDER BY AgeGroup, Total;

----prevalence of chronic medical conditions by different gender
SELECT 
    Gender, h.ChronicMedicalConditions,
    COUNT(*) AS Total,
	ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals i
JOIN HealthProfile h ON i.IndividualID = h.IndividualID
WHERE h.ChronicMedicalConditions = 'Yes'
GROUP BY 
    Gender, h.ChronicMedicalConditions
ORDER BY Total DESC;

----prevalence of chronic medical conditions by Marital Status
SELECT 
    MaritalStatus, h.ChronicMedicalConditions,
    COUNT(*) AS Total,
	ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals i
JOIN HealthProfile h ON i.IndividualID = h.IndividualID
WHERE h.ChronicMedicalConditions = 'Yes'
GROUP BY 
    MaritalStatus, h.ChronicMedicalConditions
ORDER BY Total DESC;

----prevalence of chronic medical conditions by Employment Status
SELECT 
    EmploymentStatus, h.ChronicMedicalConditions,
    COUNT(*) AS Total,
	ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals i
JOIN HealthProfile h ON i.IndividualID = h.IndividualID
WHERE h.ChronicMedicalConditions = 'Yes'
GROUP BY 
  EmploymentStatus, h.ChronicMedicalConditions
ORDER BY Total DESC;

/* Q2 How does employment status correlate with smoking, alcohol consumption, and dietary habits?*/

--Relationship between Employment Status and Smoking Status

SELECT i.EmploymentStatus, h.SmokingStatus,  COUNT(*) AS Total,
ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals i
JOIN HealthProfile h ON i.IndividualID = h.IndividualID
GROUP BY i.EmploymentStatus, h.SmokingStatus;


--Relationship between Employment Status and Alcohol Consumption 

SELECT i.EmploymentStatus,  h.AlcoholConsumption,  COUNT(*) AS Total,
ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals i
JOIN HealthProfile h ON i.IndividualID = h.IndividualID
GROUP BY i.EmploymentStatus, h.AlcoholConsumption
ORDER BY Total;

--Relationship between Employment Status and Dietary Habits

SELECT i.EmploymentStatus,  h.DietaryHabits,  COUNT(*) AS Total,
ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals i
JOIN HealthProfile h ON i.IndividualID = h.IndividualID
GROUP BY i.EmploymentStatus, h.DietaryHabits
ORDER BY Total;

/*Q3. Are there noticeable patterns in lifestyle history 
(e.g., physical activity levels, substance use) across different income brackets? */

--Income brackets and physical activity level
SELECT 
  CASE 
     WHEN Income BETWEEN 0 AND 10000 THEN '0-10,000'
     WHEN Income BETWEEN 10001 AND 60000 THEN '11,000-60,000'
	 WHEN Income BETWEEN 60001 AND 110000 THEN '61,000-110,000'
     WHEN Income BETWEEN 110001 AND 160000 THEN '111,000-160,000'
	 WHEN Income BETWEEN 160001 AND 210000 THEN '161,000-210,000'
	 ELSE '210,000+'
  END AS IncomeBracket,
  l.PhysicalActivityLevel,  COUNT(*) AS Total,
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals i
JOIN LifestyleHistory l ON i.IndividualID = l.IndividualID
GROUP BY CASE 
     WHEN Income BETWEEN 0 AND 10000 THEN '0-10,000'
     WHEN Income BETWEEN 10001 AND 60000 THEN '11,000-60,000'
	 WHEN Income BETWEEN 60001 AND 110000 THEN '61,000-110,000'
	 WHEN Income BETWEEN 110001 AND 160000 THEN '111,000-160,000'
	 WHEN Income BETWEEN 160001 AND 210000 THEN '161,000-210,000'
	 ELSE '210,000+'
  END, 
l.PhysicalActivityLevel
ORDER BY IncomeBracket;

--Income brackets and substance use

SELECT 
  CASE 
    WHEN Income BETWEEN 0 AND 10000 THEN '0-10,000'
    WHEN Income BETWEEN 10001 AND 60000 THEN '11,000-60,000'
    WHEN Income BETWEEN 60001 AND 110000 THEN '61,000-110,000'
    WHEN Income BETWEEN 110001 AND 160000 THEN '111,000-160,000'
    WHEN Income BETWEEN 160001 AND 210000 THEN '161,000-210,000'
    ELSE '210,000+'
  END AS IncomeBracket,
  l.HistoryOfSubstanceAbuse,  COUNT(*) AS Total,
  ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals i
JOIN LifestyleHistory l ON i.IndividualID = l.IndividualID
GROUP BY CASE 
     WHEN Income BETWEEN 0 AND 10000 THEN '0-10,000'
     WHEN Income BETWEEN 10001 AND 60000 THEN '11,000-60,000'
	 WHEN Income BETWEEN 60001 AND 110000 THEN '61,000-110,000'
     WHEN Income BETWEEN 110001 AND 160000 THEN '111,000-160,000'
     WHEN Income BETWEEN 160001 AND 210000 THEN '161,000-210,000'
     ELSE '210,000+'
  END, 
l.HistoryOfSubstanceAbuse
ORDER BY IncomeBracket;

/* Q4.Do individuals with higher education levels have lower rates of smoking and alcohol consumption? */

--Education level and smoking status

SELECT i.EducationLevel, h.SmokingStatus, COUNT(*) AS Total,
ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals i
JOIN HealthProfile h ON i.IndividualID = h.IndividualID
WHERE h.SmokingStatus LIKE '%Non-smoker%' 
GROUP BY i.EducationLevel, h.SmokingStatus
ORDER BY Total DESC;

--Education level and Alcohol Consumption

SELECT i.EducationLevel, h.AlcoholConsumption, 
COUNT(*) AS Total,
ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals i
JOIN HealthProfile h ON i.IndividualID = h.IndividualID
WHERE h.AlcoholConsumption LIKE '%Low%' 
GROUP BY i.EducationLevel, h.AlcoholConsumption
ORDER BY Total;

/* Q6.How does unhealthy dietary habit  vary with  education levels?*/
SELECT  i.EducationLevel, h.DietaryHabits, COUNT(*) AS Total,
ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM Individuals i
JOIN HealthProfile h ON i.IndividualID = h.IndividualID
WHERE h.DietaryHabits LIKE '%Unhealthy%'
GROUP BY  i.EducationLevel, h.DietaryHabits;


-- Q7.Do individuals with a family history of depression report more chronic medical conditions? 
SELECT L.FamilyHistoryOfDepression, h.ChronicMedicalConditions, COUNT(*) AS Total,
ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM HealthProfile h
JOIN LifestyleHistory L
ON h.IndividualID = L.IndividualID
GROUP BY L.FamilyHistoryOfDepression, h.ChronicMedicalConditions;


/* Q8. How does physical activity influence mental health outcomes among individuals with a
family history of depression? */

SELECT PhysicalActivityLevel, HistoryOfMentalIllness, COUNT(*) AS Total,
ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM LifestyleHistory 
WHERE FamilyHistoryOfDepression = 'Yes'
GROUP BY PhysicalActivityLevel, HistoryOfMentalIllness
ORDER BY PhysicalActivityLevel;

/*Q9. Are those with a family history of depression more likely to report poor dietary habits 
and low physical activity?*/

SELECT  h.DietaryHabits, l.PhysicalActivityLevel, COUNT(*) AS Total,
ROUND(CAST(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Individuals) AS DECIMAL(5,2)), 2) AS Percentage
FROM HealthProfile h
JOIN LifestyleHistory l ON h.IndividualID = l.IndividualID
WHERE l.FamilyHistoryOfDepression = 'Yes'
GROUP BY  h.DietaryHabits, l.PhysicalActivityLevel;

-- Q10. Can individuals be clustered into risk categories based on lifestyle and health indicators?
SELECT i.Gender, i.Age, i.Income, l.PhysicalActivityLevel,h.DietaryHabits, h.ChronicMedicalConditions,
       CASE 
           WHEN h.ChronicMedicalConditions = 'Yes' OR l.HistoryOfMentalIllness = 'Yes' OR l.HistoryOfSubstanceAbuse = 'Yes' 
		   THEN 'High Risk'
           ELSE 'Low Risk'
       END AS RiskCategory
FROM Individuals i
JOIN LifestyleHistory l ON i.IndividualID = l.IndividualID
JOIN HealthProfile h ON i.IndividualID = h.IndividualID;
--Summarize individuals by risklevel
WITH RiskFlags AS (
    SELECT 
        i.IndividualID,
        CASE WHEN hp.DietaryHabits = 'Unhealthy' THEN 1 ELSE 0 END +
        CASE WHEN hp.SleepPatterns = 'Poor' THEN 1 ELSE 0 END +
        CASE WHEN lh.PhysicalActivityLevel = 'Sedentary' THEN 1 ELSE 0 END +
        CASE WHEN lh.HistoryOfMentalIllness = 'Yes' THEN 1 ELSE 0 END +
        CASE WHEN lh.HistoryOfSubstanceAbuse = 'Yes' THEN 1 ELSE 0 END +
        CASE WHEN hp.ChronicMedicalConditions = 'Yes' THEN 1 ELSE 0 END 
        AS RiskScore
    FROM Individuals i
    JOIN HealthProfile hp ON i.IndividualID = hp.IndividualID
    JOIN LifestyleHistory lh ON i.IndividualID = lh.IndividualID
),
RiskCategories AS (
    SELECT 
        IndividualID,
        CASE 
            WHEN RiskScore >= 3 THEN 'High Risk'
            WHEN RiskScore <= 1 THEN 'Low Risk'
            ELSE 'Moderate Risk'
        END AS RiskLevel
    FROM RiskFlags
)

SELECT 
    RiskLevel,
    COUNT(*) AS TotalIndividuals,
    COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS Proportion
FROM RiskCategories
GROUP BY RiskLevel;


/*Q11. Which demographic factors (age, income, education) are the strongest predictors of high-risk 
mental health outcomes?*/
--By Age Group

WITH RiskFlags AS (
    SELECT 
        i.IndividualID,
        i.Age,
        i.Income,
        i.EducationLevel,
        CASE WHEN hp.DietaryHabits = 'Unhealthy' THEN 1 ELSE 0 END +
        CASE WHEN hp.SleepPatterns = 'Poor' THEN 1 ELSE 0 END +
        CASE WHEN lh.PhysicalActivityLevel = 'Sedentary' THEN 1 ELSE 0 END +
        CASE WHEN lh.HistoryOfMentalIllness = 'Yes' THEN 1 ELSE 0 END +
        CASE WHEN lh.HistoryOfSubstanceAbuse = 'Yes' THEN 1 ELSE 0 END +
        CASE WHEN hp.ChronicMedicalConditions = 'Yes' THEN 1 ELSE 0 END 
        AS RiskScore
    FROM Individuals i
    JOIN HealthProfile hp ON i.IndividualID = hp.IndividualID
    JOIN LifestyleHistory lh ON i.IndividualID = lh.IndividualID
),
RiskCategorized AS (
    SELECT 
        IndividualID,
        Age,
        Income,
        EducationLevel,
        CASE 
            WHEN RiskScore >= 3 THEN 'High Risk'
            ELSE 'Not High Risk'
        END AS RiskLevel
    FROM RiskFlags
),
AgeGrouped AS (
    SELECT *,
        CASE 
            WHEN Age BETWEEN 18 AND 27 THEN '18-27'
            WHEN Age BETWEEN 28 AND 37 THEN '28-37'
            WHEN Age BETWEEN 38 AND 47 THEN '38-47'
            WHEN Age BETWEEN 48 AND 57 THEN '48-57'
            WHEN Age BETWEEN 58 AND 67 THEN '58-67'
            WHEN Age BETWEEN 68 AND 77 THEN '68-80'
            ELSE '80+'
        END AS AgeGroup,
        CASE 
            WHEN Income BETWEEN 0 AND 10000 THEN '0-10,000'
            WHEN Income BETWEEN 10001 AND 60000 THEN '11,000-60,000'
            WHEN Income BETWEEN 60001 AND 110000 THEN '61,000-110,000'
            WHEN Income BETWEEN 110001 AND 160000 THEN '111,000-160,000'
            WHEN Income BETWEEN 160001 AND 210000 THEN '161,000-210,000'
            ELSE '210,000+'
        END AS IncomeBracket
    FROM RiskCategorized
)
SELECT 
    AgeGroup,
    COUNT(CASE WHEN RiskLevel = 'High Risk' THEN 1 END) AS HighRiskCount,
    COUNT(*) AS Total,
    ROUND(100.0 * COUNT(CASE WHEN RiskLevel = 'High Risk' THEN 1 END) / COUNT(*), 2) AS HighRiskPercent
FROM AgeGrouped
GROUP BY AgeGroup
ORDER BY HighRiskPercent DESC;

--By Income Bracket

WITH RiskFlags AS (
    SELECT 
        i.IndividualID,
        i.Age,
        i.Income,
        i.EducationLevel,
        CASE WHEN hp.DietaryHabits = 'Unhealthy' THEN 1 ELSE 0 END +
        CASE WHEN hp.SleepPatterns = 'Poor' THEN 1 ELSE 0 END +
        CASE WHEN lh.PhysicalActivityLevel = 'Sedentary' THEN 1 ELSE 0 END +
        CASE WHEN lh.HistoryOfMentalIllness = 'Yes' THEN 1 ELSE 0 END +
        CASE WHEN lh.HistoryOfSubstanceAbuse = 'Yes' THEN 1 ELSE 0 END +
        CASE WHEN hp.ChronicMedicalConditions = 'Yes' THEN 1 ELSE 0 END 
        AS RiskScore
    FROM Individuals i
    JOIN HealthProfile hp ON i.IndividualID = hp.IndividualID
    JOIN LifestyleHistory lh ON i.IndividualID = lh.IndividualID
),
RiskCategorized AS (
    SELECT 
        IndividualID,
        Age,
        Income,
        EducationLevel,
        CASE 
            WHEN RiskScore >= 3 THEN 'High Risk'
            ELSE 'Not High Risk'
        END AS RiskLevel
    FROM RiskFlags
),
AgeGrouped AS (
    SELECT *,
        CASE 
            WHEN Age BETWEEN 18 AND 27 THEN '18-27'
            WHEN Age BETWEEN 28 AND 37 THEN '28-37'
            WHEN Age BETWEEN 38 AND 47 THEN '38-47'
            WHEN Age BETWEEN 48 AND 57 THEN '48-57'
            WHEN Age BETWEEN 58 AND 67 THEN '58-67'
            WHEN Age BETWEEN 68 AND 77 THEN '68-80'
            ELSE '80+'
        END AS AgeGroup,
        CASE 
          WHEN Income BETWEEN 0 AND 10000 THEN '0-10,000'
          WHEN Income BETWEEN 10001 AND 60000 THEN '11,000-60,000'
          WHEN Income BETWEEN 60001 AND 110000 THEN '61,000-110,000'
          WHEN Income BETWEEN 110001 AND 160000 THEN '111,000-160,000'
          WHEN Income BETWEEN 160001 AND 210000 THEN '161,000-210,000'
          ELSE '210,000+'
        END AS IncomeBracket
    FROM RiskCategorized
)
SELECT 
    IncomeBracket,
    COUNT(CASE WHEN RiskLevel = 'High Risk' THEN 1 END) AS HighRiskCount,
    COUNT(*) AS Total,
    ROUND(100.0 * COUNT(CASE WHEN RiskLevel = 'High Risk' THEN 1 END) / COUNT(*), 2) AS HighRiskPercent
FROM AgeGrouped
GROUP BY IncomeBracket
ORDER BY HighRiskPercent DESC;

--By Education level 


WITH RiskFlags AS (
    SELECT 
        i.IndividualID,
        i.Age,
        i.Income,
        i.EducationLevel,
        CASE WHEN hp.DietaryHabits = 'Unhealthy' THEN 1 ELSE 0 END +
        CASE WHEN hp.SleepPatterns = 'Poor' THEN 1 ELSE 0 END +
        CASE WHEN lh.PhysicalActivityLevel = 'Sedentary' THEN 1 ELSE 0 END +
        CASE WHEN lh.HistoryOfMentalIllness = 'Yes' THEN 1 ELSE 0 END +
        CASE WHEN lh.HistoryOfSubstanceAbuse = 'Yes' THEN 1 ELSE 0 END +
        CASE WHEN hp.ChronicMedicalConditions = 'Yes' THEN 1 ELSE 0 END 
        AS RiskScore
    FROM Individuals i
    JOIN HealthProfile hp ON i.IndividualID = hp.IndividualID
    JOIN LifestyleHistory lh ON i.IndividualID = lh.IndividualID
),
RiskCategorized AS (
    SELECT 
        IndividualID,
        Age,
        Income,
        EducationLevel,
        CASE 
            WHEN RiskScore >= 3 THEN 'High Risk'
            ELSE 'Not High Risk'
        END AS RiskLevel
    FROM RiskFlags
),
AgeGrouped AS (
    SELECT *,
        CASE 
            WHEN Age BETWEEN 18 AND 27 THEN '18-27'
            WHEN Age BETWEEN 28 AND 37 THEN '28-37'
            WHEN Age BETWEEN 38 AND 47 THEN '38-47'
            WHEN Age BETWEEN 48 AND 57 THEN '48-57'
            WHEN Age BETWEEN 58 AND 67 THEN '58-67'
            WHEN Age BETWEEN 68 AND 80 THEN '68-80'
            ELSE '80+'
        END AS AgeGroup,
        CASE 
            WHEN Income BETWEEN 0 AND 10000 THEN '0-10,000'
            WHEN Income BETWEEN 10001 AND 60000 THEN '11,000-60,000'
            WHEN Income BETWEEN 60001 AND 110000 THEN '61,000-110,000'
            WHEN Income BETWEEN 110001 AND 160000 THEN '111,000-160,000'
            WHEN Income BETWEEN 160001 AND 210000 THEN '161,000-210,000'
            ELSE '210,000+'
        END AS IncomeBracket
    FROM RiskCategorized
)
SELECT 
    EducationLevel,
    COUNT(CASE WHEN RiskLevel = 'High Risk' THEN 1 END) AS HighRiskCount,
    COUNT(*) AS Total,
    ROUND(100.0 * COUNT(CASE WHEN RiskLevel = 'High Risk' THEN 1 END) / COUNT(*), 2) AS HighRiskPercent
FROM AgeGrouped
GROUP BY EducationLevel
ORDER BY HighRiskPercent DESC;
```
### Key Findings 
1. **Prevalence of Chronic Conditions:** Chronic conditions are most prevalent among older adults (68–80 years), with 6.57% of the total population in this age group reporting at least one chronic medical condition. Interestingly, younger adults (18–27 years) also show a significant prevalence of 5.46%, which is unusually high for a younger demographic. This could reflect early-onset lifestyle-related health risks or better health awareness leading to earlier diagnosis.

2. **Employment and Lifestyle Behaviors:** Employed individuals exhibited a higher prevalence of chronic conditions (20.18%) compared to their unemployed counterparts (12.74%). Furthermore, smoking and alcohol consumption were markedly higher among employed individuals, with 37,548 current smokers and 73,909 former smokers. Alcohol consumption was also more common in the employed group, particularly at moderate levels (27.91%) compared to the unemployed (14.01%).

3. **Income-Based Lifestyle Patterns:** Middle-income earners ($11,000–$60,000) showed the highest engagement in physical activity and substance use. This income group accounted for 23.19% of sedentary lifestyles and 20.81% of moderate activity. Interestingly, substance abuse was also most prevalent in this bracket, with 17.09% reporting a history of substance use, compared to 3.71% in the lowest income bracket ($0–$10,000).

4. **Education and Health Behaviors:** Higher education levels were generally linked with healthier lifestyle choices. Individuals with a Bachelor's degree had the largest representation among non-smokers (18.02%), followed by those with a High School education (16.81%). In terms of alcohol consumption, those with High School diplomas (10.49%) and Bachelor's degrees (9.59%) reported the highest rates of low alcohol use. Unhealthy dietary habits were most prevalent among individuals with only a High School education (15.42%) compared to those with tertiary education.

5. **Family History of Depression and Chronic Medical Conditions:** Individuals with a family history of depression were more likely to exhibit poor lifestyle habits. Among those with a family history, 7.18% reported both unhealthy diets and sedentary lifestyles, compared to 4.83% who reported moderate diets with low physical activity. Surprisingly, more individuals without a family history of depression (24.11%) reported chronic medical conditions, suggesting other factors may be at play.

6. **Physical Activity and Mental Health Among Individuals with Family History of Depression:** Physical activity appears to be protective against mental illness among individuals with a family history of depression. Those who maintained active lifestyles reported significantly lower rates of mental illness (1.26%) compared to those with sedentary lifestyles (3.82%).

7. **Dietary Habits and Physical Activity for Individuals with Family History of Depression:** The largest proportion (7.18%) of individuals with a family history of depression fell into the unhealthy diet + sedentary lifestyle category. This combination of poor dietary habits and low physical activity heightens vulnerability to both mental and physical health challenges.

8. **Risk Profiling Based on Lifestyle and Health Indicators:** Over one-third of the population (36.37%) fell into the high-risk category, characterized by three or more risk factors like poor diet, poor sleep, and sedentary lifestyle. The moderate-risk category accounted for 30.41%, while the low-risk group made up 33.21% of the population.

9. **Demographic Predictors of High-Risk Mental Health Outcomes:** Younger adults (18–27 years) and those in the lowest income brackets ($0–$10,000 and $11,000–$60,000) had the highest rates of high-risk mental health profiles. Individuals with lower educational attainment also exhibited increased risk levels.



### Conclusion 
The analysis reveals profound insights into the intersection of demographic characteristics, lifestyle behaviors, and health outcomes. It underscores the powerful influence that socioeconomic status, employment, education, and family history can have on mental health and chronic conditions. Notably, older adults (68–80 years) and surprisingly, younger adults (18–27 years), emerged as critical age groups for chronic condition prevalence, suggesting a need for preventive health strategies targeted at both ends of the age spectrum.

Employment status also proved to be a significant factor, with employed individuals showing higher rates of smoking and alcohol consumption. This correlation points to workplace-related stress and social behaviors that may contribute to poor health outcomes. Middle-income earners, with the financial capacity to access both healthy activities and risky substances, highlighted the complex relationship between income and lifestyle choices.

Educational attainment emerged as a protective factor against poor health behaviors, suggesting that health literacy and informed decision-making can mitigate some of the risks associated with mental health and chronic diseases. Furthermore, individuals with a family history of depression displayed notable vulnerabilities in terms of diet and physical activity, emphasizing the need for targeted wellness programs that address both genetic predispositions and environmental factors.

Overall, the findings emphasize the importance of multi-layered public health interventions that consider not just individual behaviors but also the broader social determinants of health. Addressing these factors holistically could lead to more effective strategies for preventing and managing mental health challenges and chronic conditions within communities.

### Recommendations
To effectively address the multifaceted health challenges identified, targeted interventions should be designed considering demographic, socioeconomic, and lifestyle factors.
Health promotion strategies should prioritize younger adults (18–27 years) who unexpectedly exhibit a high prevalence of chronic conditions. These programs should focus on lifestyle modification, early detection, and management of chronic diseases through community outreach and digital health education, especially targeting age-specific risks.
To address the higher prevalence of chronic conditions among females, gender-responsive healthcare policies should be developed. These could include routine screenings, mental health support, and lifestyle counseling tailored to women’s health needs, particularly focusing on the mid-to-late adulthood phase where risks increase.
Employment-focused health programs should address the higher rates of smoking and alcohol consumption among employed individuals. Workplace wellness initiatives should include smoking cessation support, stress management workshops, and healthier food options at the workplace. Simultaneously, unemployed individuals require accessible community-based health programs that offer smoking cessation resources, nutrition counseling, and stress management support, regardless of their economic status.
Income disparities impacting physical activity levels suggest the need for subsidized or free access to recreational facilities and structured exercise programs for low-income communities. Additionally, nutrition assistance programs should focus on improving access to affordable, healthy food options, particularly for low-income households that are more likely to consume unhealthy diets.
Educational initiatives are essential in promoting health literacy, particularly among individuals with low formal education. Community health workshops focusing on smoking cessation, healthy eating, and mental well-being should be tailored to this group, using simple, culturally relevant messaging.
Mental health interventions should particularly target individuals with a family history of depression who are prone to unhealthy diets and sedentary behavior. Integrated health programs that combine physical activity promotion, dietary counseling, and mental health support can help mitigate the compounded risk factors faced by this group.
For the high-risk population identified through the lifestyle risk profiling, a multi-pronged approach is needed. These individuals would benefit from comprehensive lifestyle intervention programs that address smoking, alcohol consumption, poor dietary habits, and physical inactivity simultaneously. Regular follow-ups and personalized support plans can help sustain positive changes over time.
Lastly, public health policies should address the socioeconomic determinants of health by reducing financial barriers to healthcare access, promoting workplace wellness, and providing education on preventive health practices. Empowering individuals through education, accessible healthcare, and community support systems is essential for reducing the overall burden of chronic conditions and improving mental health outcomes.



### Limitations 
Self-reported Data Bias: The reliance on self-reported data for lifestyle behaviors introduces risks of recall bias and social desirability bias.

Cross-Sectional Analysis: The data is cross-sectional, which limits the ability to infer causal relationships.

Synthetic Dataset: As the dataset is synthetic, it may not fully capture real-world complexities, although it provides a structured environment for hypothesis testing.

### Future Work

Integrating predictive models using machine learning to enhance the accuracy of risk profiling.

Extending the analysis to real-world datasets for validation and broader applicability.

Incorporating data visualization techniques to better communicate findings and engage stakeholders.
