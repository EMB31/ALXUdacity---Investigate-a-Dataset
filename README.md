# ALXUdacity---Investigate-a-Dataset
My first python project was an introduction to data analysis using python. 
For this project, i had to learn to wrangle data, perform exploratory Data analysis and draw conclusions from analysis done.

## Table of Contents

* [Introduction](#introduction)
* [Data Cleaning](#data-cleaning)
* [Exploratory Data Analysis](#exploratory-data-analysis)
* [Conclusions](#conclusions)


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
EDA was performed to answer the questions outlined in the beginning.

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

### Which location accounts for the most No Shows?
```
#create a dataframe for appointments where patient did not show up
df_missed = df.query('NoShow =="Yes"')

#Count the number of occurrences of each neighbourhood value in the no show dataframe df_noshow 
#Since I am only interested in the neighbourhood that accounts for the most no shows 
#I limited the data to the highest 10 values
NoOfOccurrences = df_missed.groupby('Neighbourhood')['NoShow'].count().nlargest(10)

#plot horizontal bar chart showing number of no shows per neigbourhood 
NoOfOccurrences.plot(kind='barh', figsize =(10,8), color="#488AC7");
plt.title("Number of no shows per Neighbourhood",weight ='bold')
plt.xlabel("Number of no shows", labelpad=15, weight='bold',size=12)
plt.ylabel("Neighbourhood",labelpad=15, weight='bold',size=12);
```
![image](https://user-images.githubusercontent.com/113180085/201522419-34e748b3-03b7-4724-a099-11f1087af25e.png)

### Determine if the Gender, Scholarship status, Age and Patient's condition have any influence on showing up to their appointment
```
# Use table containing info on only missed appointment i.e. df_noshow that was created earlier
# Count the NoShow column values and group by gender
df_gender_missed = df_missed.groupby('Gender')['NoShow'].count()

# Filter table for only instances where patient showed up
df_showedup = df.query('NoShow =="No"')

#Count the NoShow column and group by gender
df_gender_showedup = df_showedup.groupby('Gender')['NoShow'].count()

#To plot the grouped bar chart
# define the width of the bars
wth = 0.3

#define the distinct gender labels
gender =['Female', 'Male']

#Define position of the bars
bar1_psn=np.arange(len(gender))
bar2_psn=[i+wth for i in bar1_psn]

#plot the bar charts
plt.bar(bar1_psn,df_gender_showedup,wth,label="showed up");
plt.bar(bar2_psn,df_gender_missed,wth,label="missed");

#change the x-axis label to the gender values and position it at the center
plt.xticks(bar1_psn+wth/2,gender)

#show the legend
plt.legend();

#add axis labels and title
plt.title("Appointment attendance per gender",weight ='bold')
plt.xlabel("Gender", labelpad=15, weight='bold',size=12)
plt.ylabel("Count of attendance",labelpad=15, weight='bold',size=12);
```
![image](https://user-images.githubusercontent.com/113180085/201522576-fec809ee-087c-42ea-b623-daa7e58f50bd.png)

```
#Check number of patients enrolled in the Bolsa Familia scholarship
print (df['Scholarship'].value_counts())

#Check number of scholarship receipients who missed their appointments
print (df_missed['Scholarship'].value_counts())

#Count number of missed appointments and group by scholarship status
df_sch_missed =df_missed.groupby('Scholarship')['NoShow'].count()

#Count number of appointments attended and group by scholarship status
df_sch_attend = df_showedup.groupby('Scholarship')['NoShow'].count()

#define the scholarship status labels
sch_values=['Not Enrolled', 'Enrolled']
#plt.bar(sch_values,df_sch_missed, wth);

#Define position of the bars
bar1_psn=np.arange(len(sch_values))
bar2_psn=[i+wth for i in bar1_psn]

#plot the bar charts
plt.bar(bar1_psn,df_sch_attend,wth,label="showed up");
plt.bar(bar2_psn,df_sch_missed,wth,label="missed");

#change the x-axis label to the scholarship status values and position it at the center
plt.xticks(bar1_psn+wth/2,sch_values)

#show the legend
plt.legend();

#add axis labels and title
plt.title("Patients' appointment attendance based on Scholarship status",weight ='bold')
plt.xlabel("Scholarship Status", labelpad=15, weight='bold',size=12)
```
![image](https://user-images.githubusercontent.com/113180085/201522667-cb27e9a3-3fc0-41bd-8e32-24925cfecd33.png)

## Conclusions
From the EDA conducted, I was able to conclude that there were relatively less appointments missed. Of the 110527 appointments scheduled, 22319 were missed; which accounts for 20.19% of the appointments scheduled.

Of the over 22000 missed appointments, Jardin Camburi accounts for about 1500 of them. Other neighbourhoods such as Maria Ortiz, Itarare, Resistencia, Cemtro, etc also account for the highest numbers of these missed appointments.

Out of 81 distinct locations, 80 of them have recorded instances of missed appointments. Unfortunately, there isn't additional data such as the ease of accessing the hospital, service delivery rating, etc to ascertain if missing appointments is influenced by the hospital service or the patient's ease of accessing the hospital.
I also noticed that majority of the patients are females, however both males and females fairly miss and attend appointments. As such it is not easy to conclude if the gender of the patient has any influence of them showing up for the appointment.

Also, with the availability of the Bolsa Familia welfare program, I noticed that majority of the patients are not enrolled. Unfortunately there isn't enough information to determine why there are few enrolled on the program or if there are any special criteria for it. I noticed, however, that enrolment in the scholarship doesn't have much of an impact on attendance either. In fact of the may patients not enrolled in the program, majority of them attend their appointments.
