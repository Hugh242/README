Predicting Senior Food Insecurity for Wesley Life Expansion

Project Overview

The goal of this project is to predict senior food insecurity at the PUMA (Public Use Microdata Area) level to help Wesley Life identify areas in Iowa with the highest need for expansion. Using CPS data for training and ACS data for real-world predictions, the project builds predictive models that analyze household characteristics and aggregate findings at the PUMA level. This effort provides actionable insights into food insecurity patterns. 

Data Sources and Preparation

1.	Data
	•	CPS Data: Contains food insecurity indicators and demographic details, sourced from IPUMS CPS.
	•	ACS Data: Includes demographic information for Iowa households at the PUMA level, accessed from Census ACS.

This project uses three data sources, all located in the data/ directory. Ensure all files are downloaded and stored in the correct folder before running the code:
	•	spm_pu_2022.sas7bdat: ACS survey data for Iowa PUMAs, providing household-level data to predict food insecurity probabilities.
	•	cps_00006.csv: CPS survey data with food insecurity measures, used for training the models.
	•	total_iowa_seniors_by_puma: Total seniors per PUMA by GEOID.

Preparation Steps:
 CPS Data:
	•	Cleaned individual-level data.
	•	Aggregated household-level variables (e.g., household size, elderly presence).
	•	Addressed missing values and created dummy variables for predictive modeling.
 ACS Data:
	•	Transformed variables to match CPS definitions for compatibility.
	•	Aggregated data to the household level and applied survey weights.
 Packages Required:
	•	"tidyverse", "ggthemes", "logistf", "glmnet", "haven", "dplyr", 
                    "fastDummies", "tigris", "sf", "knitr", "kableExtra", "pROC"
	•	Install with: install.packages(c("tidyverse", "ggthemes", "logistf", "glmnet", "haven", "dplyr", 
                    "fastDummies", "tigris", "sf", "knitr", "kableExtra", "pROC")).

Reproducibility: Execute These R Files in Order

1.	Environment Setup
	•	Clear the R environment using rm(list = ls()).
	•	Ensure all required code and data files are downloaded and placed in the working directory.
	
	
2.	clean_CPS.R
	•	Loads CPS data (data/cps_00006.csv) and generates variables aligned with food insecurity prediction.
	•	Cleans and aggregates data at the household level.
	•	Constructs additional variables relevant for predicting food insecurity.
	
	
3.	clean_ACS.R
	•	Processes ACS data (data/spm_pu_2022.sas7bdat) to create variables comparable to those in the CPS dataset.
	
	
4.	Model 1
	•	Initiates by sourcing clean_CPS.R, loading libraries, and cleaning the dataset.
	•	Defines food insecurity (FSFOODS) as the response variable, indicating households lacking sufficient food.
	•	Splits the data into training and testing sets, removes missing values, and generates key variables:
	•	donut: Identifies households with only seniors and children.
	•	edu_working: Indicates the presence of a working adult with a college education.
	•	single_parent: Identifies single-parent households.
	•	Develops three logistic regression models (no interactions, one interaction, multiple interactions), employing Lasso and Ridge regression with cross-validated lambda selection.
	•	ROC curves and AUC metrics evaluate model performance, identifying the Lasso model as optimal.
	•	Sources clean_ACS.R to apply the best-performing model to ACS data, generating predictions and merging with PUMA data.
	•	Results are visualized using heat maps to highlight regions with high senior food insecurity rates.
	
	
5.	Model 2
	•	Also begins with clean_CPS.R, setting the response variable (FSFOODS2) to represent broader food insecurity, including limited food variety.
	•	Follows a similar process to Model 1, generating the same key variables and developing logistic regression models.
	•	ROC curves and AUC metrics identify Ridge regression as optimal for this context.
	•	Predictions are applied to ACS data to identify affected PUMAs, with results visualized via heat maps.
	
	
6.	Model 3
	•	Begins by sourcing clean_CPS.R and splitting the data into training and testing sets.
	•	Removes missing values, converts datasets to matrices, and transforms response variables into vectors.
	•	Develops four models using gamma and inverse Gaussian distributions:
	•	gamlasso: Lasso with gamma distribution.
	•	gamridge: Ridge with gamma distribution.
	•	iglasso: Lasso with inverse Gaussian distribution.
	•	igridge: Ridge with inverse Gaussian distribution.
	•	After training, optimal lambdas are selected, and RMSE and  R^2  values are computed.
	•	Inverse Gaussian models are excluded due to non-convergence.
	•	Lasso with gamma distribution is identified as the most effective model for out-of-sample prediction.
	•	The model is applied to ACS data to predict PUMAs with significant senior populations and lower food expenditures per person.

4. Explanation of Methods

This project utilizes Lasso and Ridge regression within logistic frameworks to predict food insecurity in Iowa PUMAs. These methods are well-suited for managing multicollinearity and mitigating overfitting:
	•	Lasso regression selects features by shrinking irrelevant coefficients to zero.
	•	Ridge regression stabilizes estimates for highly correlated predictors.

Three complex models were evaluated using ROC curves and AUC scores. The top-performing models were then applied to ACS data to produce predictions, with results visualized through heat maps to support the strategic expansion of Meals-on-Wheels services.
