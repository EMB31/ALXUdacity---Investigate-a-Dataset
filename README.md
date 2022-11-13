# ALXUdacity---Investigate-a-Dataset
My first python project was an introduction to data analysis using python. 
For this project, i had to learn to wrangle data, perform exploratory Data analysis and draw conclusions from analysis done.

## Introduction
The dataset used for the project was the No Show Appointments dataset. This dataset contains information about some 100,000 medical appointment schedules across different hospitals in Brazil and whether the patient showed up for said appointment. The dataset contains the following columns:
- **PatientId** - the Patients's ID number
- **AppointmentId** - the appointment ID number
- **Gender** - the patient's gender
- **ScheduleDay** - what day the patient scheduled the appointment
- **AppointmentDay** - the expected day for the appointment
- **Neighbourhood** - the location of the hospital
- **Scholarship** - indicates whether the patient is enrolled in the Brasilian welfare program Bolsa Família as 0s (for those not enrolled) and 1s (enrolled patients)
- **Hipertension, Diabetes, Alcoholism and Handicap**  - These columns indicate the presence of any of these conditions as 0s and 1s
- **SMS_received** - indicates receipt of SMS notification
- **No-show** - indicates whether the patient missed their appointment. *Yes* means they missed the appointment and *No* means they showed up.

To investigate the dataset I had to think of what i wanted to find out from the data. The Questions I wanted to answer were as follows:
1. Ratio of no shows to show ups: Are there more cases of no shows against show ups?
2. Which location accounts for the most no shows?
3. Determine if any of the following factors influence patients missing their appointment:
    - Gender; Is the gender of the patient likely to influence them showing up for the appointment?
    - Scholarship; Are the welfare recipients more likely to show up for their appointments?
    
 Following this I imported the necessary libraries required for the project:
 ```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
```

## Data cleaning
the required dataset was first loaded into a dataframe:
```
df = pd.read_csv('noshowappointments.csv')
df.head()
```

In order to clean data i first assessed it using the info method to check for
- null values
- datatypes of the various columns
- column naming errors

```
df.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 110527 entries, 0 to 110526
Data columns (total 14 columns):
PatientId         110527 non-null float64
AppointmentID     110527 non-null int64
Gender            110527 non-null object
ScheduledDay      110527 non-null object
AppointmentDay    110527 non-null object
Age               110527 non-null int64
Neighbourhood     110527 non-null object
Scholarship       110527 non-null int64
Hipertension      110527 non-null int64
Diabetes          110527 non-null int64
Alcoholism        110527 non-null int64
Handcap           110527 non-null int64
SMS_received      110527 non-null int64
No-show           110527 non-null object
dtypes: float64(1), int64(8), object(5)
memory usage: 11.8+ MB
```
There were no null values in this dataset. 
However, the PatientId column is a float, and will be changed to an integer value in a subsequent cell since it is an Id number.
The ScheduledDay and AppointmentDay columns will also be converted to datetime columns.

The 'Handcap' and 'Hipertension' columns will be renamed because they are incorrectly spelt. 
The No-show column will be renamed for consistency and ease of use.

Other relevant methods were used to check for duplicate values, amongst others.
```
sum(df.duplicated())
0

df.describe()
```
![image](https://user-images.githubusercontent.com/113180085/201521486-e9bfdc7a-634d-4b0c-82f0-8e54ae032735.png)

There were no null values in the dataset. However the minimum age was a negative value which had to be corrected.

### Rectifying issues observed
```
# Convert PatientId to an integer
# int64 is specified to prevent Id number from being cut off
df['PatientId'] =df['PatientId'].astype('int64')

#Convert SceduledDay and AppointmentDay to datetime columns
df['ScheduledDay'] =pd.to_datetime(df['ScheduledDay'])
df['AppointmentDay'] =pd.to_datetime(df['AppointmentDay'])

#Change column names
NewNames = {'Hipertension':'Hypertension','Handcap':'Handicap','No-show':'NoShow'}
df= df.rename(columns = NewNames)

#Convert age column to positive values only
df['Age'] = df['Age'].abs()
```
The info and describe methods were used again to confirm that the changes have taken effect.

```
df.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 110527 entries, 0 to 110526
Data columns (total 14 columns):
PatientId         110527 non-null int64
AppointmentID     110527 non-null int64
Gender            110527 non-null object
ScheduledDay      110527 non-null datetime64[ns]
AppointmentDay    110527 non-null datetime64[ns]
Age               110527 non-null int64
Neighbourhood     110527 non-null object
Scholarship       110527 non-null int64
Hypertension      110527 non-null int64
Diabetes          110527 non-null int64
Alcoholism        110527 non-null int64
Handicap          110527 non-null int64
SMS_received      110527 non-null int64
NoShow            110527 non-null object
dtypes: datetime64[ns](2), int64(9), object(3)
memory usage: 11.8+ MB

df['Age'].describe()
count    110527.000000
mean         37.088892
std          23.110176
min           0.000000
25%          18.000000
50%          37.000000
75%          55.000000
max         115.000000
Name: Age, dtype: float64
```

The SMS_received column was dropped because its purpose was unclear.
```
del df['SMS_received']
```

## Exploratory Data Analysis

### Are there more cases of no shows against show ups?
```
#Display value counts of the noshow column
CntOfNoShowVal = df['NoShow'].value_counts()

#Use the plot function to create a pie chart
#The label attribute is to remove the default label appended to the chart
#The explode attribute is used to separate the sections from each other
#autopct was used to show the percentage values to 2 decimal places
chart = CntOfNoShowVal.plot(kind = 'pie', figsize = (6,6),label=' ', explode=[0,0.2], autopct='%1.2f%%');

#Add a title
chart.set_title("Ratio of show ups to no shows", weight='bold');
```
![image](https://user-images.githubusercontent.com/113180085/201522142-e7f322ed-4aef-4614-810c-6d821d973182.png)
