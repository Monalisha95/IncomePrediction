**Problem Statement**

To build a classification methodology to determine whether a person makes over 50K per year.

**Architecture**

` `![](Aspose.Words.d8ca5e8a-f17e-4582-9bd7-e000d7c0e677.001.jpeg)

**Data Description**

The client will send data in multiple sets of files in batches at a given location. The data has been extracted from the census bureau. 

The data contains 32561 instances with the following attributes:

**Features:**

1. **age**: continuous. It denotes the age of the person.
1. **workclass**: It denotes the working class of the person. Sample values: Private, Self-emp-not-inc, Self-emp-inc, Federal-gov, Local-gov, State-gov, Without-pay, Never-worked.
1. **fnlwgt**: continuous.
1. **education**: It denotes the educational qualification of the person. Sample values: Bachelors, Some-college, 11th, HS-grad, Prof-school, Assoc-acdm, Assoc-voc, 9th, 7th-8th, 12th, Masters, 1st-4th, 10th, Doctorate, 5th-6th, Preschool.
1. **education**-num: continuous. It denotes the quantitative values with reference to education. 
1. **marital-status**: It denotes the marital status of the person. Sample values:  Married-civ-spouse, Divorced, Never-married, Separated, Widowed, Married-spouse-absent, Married-AF-spouse.
1. **occupation**: It denotes the occupation of a person. Sample values: Tech-support, Craft-repair, Other-service, Sales, Exec-managerial, Prof-specialty, Handlers-cleaners, Machine-op-inspct, Adm-clerical, Farming-fishing, Transport-moving, Priv-house-serv, Protective-serv, Armed-Forces.
1. **relationship**: It denotes the people present in the family. Sample values: Wife, Own-child, Husband, Not-in-family, Other-relative, Unmarried.
1. **race**: It denotes the person’s origins. Sample values: White, Asian-Pac-Islander, Amer-Indian-Eskimo, Other, Black.
1. **sex**: It denotes the person's gender. Sample values: Female, Male. 
1. **capital-gain**: continuous. It denotes the monitory gains by the person.
1. **capital-loss:** continuous. It denotes the monitory loss by the person.
1. **hours-per-week:** continuous. It denotes the number of working hours per week by the person.
1. **native-country:** It denotes the country to which the person belongs. Sample values: United-States, Cambodia, England, Puerto-Rico, Canada, Germany, Outlying-US(Guam-USVI-etc), India, Japan, Greece, South, China, Cuba, Iran, Honduras, Philippines, Italy, Poland, Jamaica, Vietnam, Mexico, Portugal, Ireland, France, Dominican-Republic, Laos, Ecuador, Taiwan, Haiti, Columbia, Hungary, Guatemala, Nicaragua, Scotland, Thailand, Yugoslavia, El-Salvador, Trinadad&Tobago, Peru, Hong, Holand-Netherlands.

**Target Label:**

Whether a person earns more or less than 50000 dollars per year.

1. Income:**  >50K, <=50K.

Apart from training files, we also require a "schema" file from the client, which contains all the relevant information about the training files such as:

Name of the files, Length of Date value in FileName, Length of Time value in FileName, Number of Columns, Name of the Columns, and their datatype.

**Data Validation** 

In this step, we perform different sets of validation on the given set of training files.  

1. ` `Name Validation- We validate the name of the files based on the given name in the schema file. We have created a regex pattern as per the name given in the schema file to use for validation. After validating the pattern in the name, we check for the length of date in the file name as well as the length of time in the file name. If all the values are as per requirement, we move such files to "Good\_Data\_Folder" else we move such files to "Bad\_Data\_Folder."

1. ` `Number of Columns - We validate the number of columns present in the files, and if it doesn't match with the value given in the schema file, then the file is moved to "Bad\_Data\_Folder."


1. ` `Name of Columns - The name of the columns is validated and should be the same as given in the schema file. If not, then the file is moved to "Bad\_Data\_Folder".

1. ` `The datatype of columns - The datatype of columns is given in the schema file. It is validated when we insert the files into Database. If the datatype is wrong, then the file is moved to "Bad\_Data\_Folder".


1. Null values in columns - If any of the columns in a file have all the values as NULL or missing, we discard such a file and move it to "Bad\_Data\_Folder".

**Data Insertion in Database**



\1) Database Creation and connection - Create a database with the given name passed. If the database has already been created, open a connection to the database. 

\2) Table creation in the database - Table with name - "Good\_Data", is created in the database for inserting the files in the "Good\_Data\_Folder" based on given column names and datatype in the schema file. If the table is already present, then the new table is not created, and new files are inserted in the already present table as we want training to be done on new as well as old training files.     

\3) Insertion of files in the table - All the files in the "Good\_Data\_Folder" are inserted in the above-created table. If any file has invalid data type in any of the columns, the file is not loaded in the table and is moved to "Bad\_Data\_Folder".

**Model Training** 

\1) **Data Export from Db** - The data in a stored database is exported as a CSV file to be used for model training.

\2) **Data Preprocessing**   

1) Drop the columns not required for prediction.
1) Remove the unwanted spaces in data.
1) For this dataset, the null values were replaced with ‘?’ in the client data. Those ‘?’ have been replaced with NaN values.
1) Check for null values in the columns. If present, impute the null values using the categorical imputer.
1) Replace and encode the categorical values with numeric values.
1) Scale the numeric values using the standard scaler.
1) Handle the imbalanced dataset using oversampling.

\3) **Clustering -** KMeans algorithm is used to create clusters in the preprocessed data. The optimum number of clusters is selected by plotting the elbow plot, and for the dynamic selection of the number of clusters, we are using "KneeLocator" function. The idea behind clustering is to implement different algorithms

The Kmeans model is trained over preprocessed data, and the model is saved for further use in prediction.

\4) **Model Selection –** After the clusters have been created, we find the best model for each cluster. We are using two algorithms, "Random Forest" and "XGBoost". For each cluster, both the algorithms are passed with the best parameters derived from GridSearch. We calculate the AUC scores for both models and select the model with the best score. Similarly, the model is selected for each cluster. All the models for every cluster are saved for use in prediction.




**Prediction Data Description**

` `The Client will send the data in multiple sets of files in batches at a given location. Data will contain the annual income of various persons. 

Apart from prediction files, we also require a "schema" file from the client, which contains all the relevant information about the training files such as:

Name of the files, Length of Date value in FileName, Length of Time value in FileName, Number of Columns, Name of the Columns and their datatype.

**Data Validation**  

In this step, we perform different sets of validation on the given set of training files.  

\1) Name Validation- We validate the name of the files based on given Name in the schema file. We have created a regex pattern as per the name given in the schema file, to use for validation. After validating the pattern in the name, we check for the length of date in the file name as well as the length of the timestamp in the file name. If all the values are as per requirement, we move such files to "Good\_Data\_Folder" else we move such files to "Bad\_Data\_Folder". 

\2) Number of Columns - We validate the number of columns present in the files, and if it doesn't match with the value given in the schema file, then the file is moved to "Bad\_Data\_Folder". 

\3) Name of Columns - The name of the columns is validated and should be same as given in the schema file. If not, then the file is moved to "Bad\_Data\_Folder". 

\4) Datatype of columns - The datatype of columns is given in the schema file. This is validated when we insert the files into Database. If the datatype is incorrect, then the file is moved to "Bad\_Data\_Folder". 

\5) Null values in columns - If any of the columns in a file has all the values as NULL or missing, we discard such file and move it to "Bad\_Data\_Folder". 

**Data Insertion in Database** 

\1) Database Creation and connection - Create a database with the given name passed. If the database is already created, open the connection to the database. 

\2) Table creation in the database - Table with name - "Good\_Data", is created in the database for inserting the files in the "Good\_Data\_Folder" based on given column names and datatype in the schema file. If the table is already present, then a new table is not created, and new files are inserted into the already present table as we want training to be done on new as well old training files.     

\3) Insertion of files in the table - All the files in the "Good\_Data\_Folder" are inserted in the above-created table. If any file has invalid data type in any of the columns, the file is not loaded in the table and is moved to "Bad\_Data\_Folder".

**Prediction** 

\1) Data Export from Db - The data in the stored database is exported as a CSV file to be used for prediction.

\2) Data Preprocessing  :  

1) Drop the columns not required for prediction.
1) Remove the unwanted spaces in data.
1) For this dataset, the null values were replaced with ‘?’ in the client data. Those ‘?’ have been replaced with NaN values.
1) Check for null values in the columns. If present, impute the null values using the categorical imputer.
1) Replace and encode the categorical values with numeric values.
1) Scale the numeric values using the standard scaler.
1) Handle the imbalanced dataset using oversampling

\3) Clustering - KMeans model created during training is loaded, and clusters for the preprocessed prediction data is predicted.

\4) Prediction - Based on the cluster number, the respective model is loaded and is used to predict the data for that cluster.

\5) Once the prediction is made for all the clusters, the predictions along with the Wafer names are saved in a CSV file at a given location, and the location is returned to the client.

**Deployment**

We will be deploying the model to the Pivotal Cloud Foundry platform. 
