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

As part of cleaning the data I checked for the following using the info method
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

Other relevant methods were used to check for duplicate values, amongst others.
```
sum(df.duplicated())
0

df.describe()
![image](https://user-images.githubusercontent.com/113180085/201521086-282ffaa0-ab4f-48e3-b399-033951c340e4.png)

```
