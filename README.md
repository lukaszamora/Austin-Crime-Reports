# Austin-Crime-Reports

Exploratory data analysis of Austin Police Department's crime reports.

The data was gathered from APD's [Crime Reports](https://data.austintexas.gov/Public-Safety/Crime-Reports/fdj4-gpfu) database. It ranges in crimes committed from 2003 to August 2020. 

## 1 - Introduction/Problem Definition

To have a perspective of the state of security of Austin I defined few questions, which I answered during this data analytic project. Here is the list of these questions:
1. How has the number of various crimes changed over time in Austin?
2. How have the number arrests corresponded to the crimes changed over time in Austin?
3. Which crimes are most frequently committed?
4. Which locations are these frequent crimes being committed to?
5. Are there certain high crime locations for certain crimes?
6. How has the number of certain crimes changed over the years in Austin?

To answer these question I took the four main steps of the KDD data mining pipeline, which are respectively, data preprocessing, data pre-processing, analysis and post-processing. In this documentation, I also use the same name for each section of the report. In Section 2, I describe how I gathered our data and the tasks I did in regard to clean the data as the data Pre-Processing phase. Section 3 I dive straight into the data analysis process, firstly, introducing the methods and technologies I used and then provide details on how I dealt with crime data in Spark SQL. Section 4 dives into the visualization of my results. Finally, in Section 5 I bring the conclusion.

![kdd](images/kdd.JPG)

## 2 - Data Pre-Processing: Data Extraction

### 2.1 - Data Exploration

The dataset was gathered from [data.austin.gov](https://data.austintexas.gov/). According to the information provided along with this dataset, 

> This dataset contains a record of incidents that the Austin Police Department responded to and wrote a report. Please note one incident may have several offenses associated with it, but this dataset only depicts the highest level offense of that incident. Data is from 2003 to present. This dataset is updated weekly. Understanding the following conditions will allow you to get the most out of the data provided. Due to the methodological differences in data collection, different data sources may produce different results. This database is updated weekly, and a similar or same search done on different dates can produce different results. Comparisons should not be made between numbers generated with this database to any other official police reports. Data provided represents only calls for police service where a report was written. Totals in the database may vary considerably from official totals following investigation and final categorization. Therefore, the data should not be used for comparisons with Uniform Crime Report statistics.

In general, the data included information such as data/time the crime was committed, the location where the crime occurred, type of crime, location description, whether there was an arrest, and location coordinates.

#### 2.1.1 - Size of Data

The data had 2,255,966 records and 27 columns. The list of the names of each column from left to right are as follows: Incident Number, Highest Offense Description, Highest Offense Code, Family Violence, Occurred Date Time, Occurred Date, Occurred Time, Report Date Time, Report Date, Report Time, Location Type, Address, Zip Code, Council District, APD Sector, APD District, PRA, Census Tract, Clearance Status, Clearance Date, UCR Category, Category Description, X-coordinate, Y-coordinate, Latitude, Longitude, and Location.

![dataset](images/figure1.JPG)

#### 2.1.2 - Sneak Peek into the Data

To have a quick intrusion of the structure of this dataset, here is a screenshot of the first few records. 

![peek](images/figure2.JPG)

### 2.2 - Data Extraction

There are so many tools for data Preprocessing like Stanford Visualization Groups Data Wrangler, Redshift, or OpenRefine. These tools are fantastic and can save hours. There is overlap in their functionality as well. However, Spark SQL is incredibly easy and convenient; I used this tool to gain an insight into the data. Using Spark we find out that based on our goal in the project, the Austin Crime dataset requires one of the most important data pre-processing procedure which is *cleaning*. Our data needs to be cleansed by:

* Removing duplicate rows
* Removing missing values (NULL/NA values) in the dataset
* Fltering out all the features from the dataset that are not relevant to our data analysis (UCR Category, APD Sector, etc.)

To apply these preprocessing tasks on our dataset, I used Spark in this sequence:  First, I had a lot of corrupted data in each record that I had to remove. For instance, out of 2,255,966 records in the file, 70,627 records were filtered due to not matching with the column attribute. After that, I had to find the wrong data in each column and remove them. Then I removed the duplicate data, and finally, delete the columns I did not need so the data would be smaller and faster to work with. After making sure the data is clean. Doing such clearing, lowered the size of the data significantly.

## Analysis

As explained in a previous section, I used Spark SQL (more specifically, the `pyspark` Python library) to query to the data set to answer each of the questions I defined from the project’s goal in Section 1. 

#### Question 1: How has the number of various crimes changed over time in Austin?

From this question, we can view how each type of crime has been decreasing or increasing over the years. The following Spark SQL code will output this table as a result.

```
df_clean = df.withColumn('year',f.year(f.to_timestamp('occurred_date','MM/dd/yyyy')))

crime_count = df_clean.groupBy("highest_offense_description", "year") \
              .count().orderBy("year","highest_offense_description",ascending=True) \
              .show(20,False)
```

```
+------------------------------+----+-----+
|highest_offense_description   |year|count|
+------------------------------+----+-----+
|ABUSE OF OFFICIAL CAPACITY    |2003|1    |
|AGG ASLT W/MOTOR VEH FAM/DAT V|2003|9    |
|AGG ASSAULT                   |2003|327  |
|AGG ASSAULT FAM/DATE VIOLENCE |2003|258  |
|AGG ASSAULT ON PUBLIC SERVANT |2003|15   |
|AGG ASSAULT WITH MOTOR VEH    |2003|47   |
|AGG FORCED SODOMY             |2003|2    |
|AGG FORCED SODOMY OF CHILD    |2003|32   |
|AGG KIDNAPPING                |2003|2    |
|AGG PERJURY                   |2003|1    |
|AGG PROMOTION OF PROSTITUTION |2003|7    |
|AGG RAPE                      |2003|6    |
|AGG RAPE OF A CHILD           |2003|22   |
|AGG ROBBERY BY ASSAULT        |2003|19   |
|AGG ROBBERY/DEADLY WEAPON     |2003|234  |
|AGG SEXUAL ASSAULT CHILD/OBJEC|2003|37   |
|AGG SEXUAL ASSAULT W OBJECT   |2003|11   |
|AMPLIFIED MUSIC / VEHICLE     |2003|4    |
|APPLIC TO REVOKE PROBATION    |2003|25   |
|ARSON                         |2003|68   |
+------------------------------+----+-----+
only showing top 20 rows
```

#### Question 2: How have the number of arrests corresponding to the crimes changed over time in Austin?

By calculating the total amount of crimes and arrest each year, we can see how many crimes were solved and see how the crime occurred in Austin during 2003-2020. The following SQL lets us get the number of crimes and arrest each year.

```
   crimes           arrests
+----+-----+      +----+-----+
|year|count|      |year|count|
+----+-----+      +----+-----+
|2003|62793|      |2003|13097|
|2004|60785|      |2004|15174|
|2005|64123|      |2005|14645|
|2006|64605|      |2006|14385|
|2007|68777|      |2007|15282|
|2008|71728|      |2008|17830|
|2009|70136|      |2009|16800|
|2010|67573|      |2010|15169|
|2011|63425|      |2011|14303|
|2012|62052|      |2012|13921|
|2013|59334|      |2013|13261|
|2014|55839|      |2014|12446|
|2015|52814|      |2015|10925|
|2016|50760|      |2016|10876|
|2017|48053|      |2017|10544|
|2018|45306|      |2018|8798 |
|2019|53457|      |2019|8738 |
|2020|27001|      |2020|3722 |
+----+-----+      +----+-----+
```

#### Question 3: Which crimes are frequently committed?

More specifically in this question, we want to know what the most frequent crime is committed between the years 2003 and 2020, so we need to calculate the number of each crime type during all these years. To find the result we used the following Spark SQL command:

```
top_crimes = df_clean.groupBy('highest_offense_description').count().orderBy(desc("count")).show(20, False)
```

```
+------------------------------+------+
|highest_offense_description   |count |
+------------------------------+------+
|BURGLARY OF VEHICLE           |107116|
|FAMILY DISTURBANCE            |99369 |
|THEFT                         |82773 |
|CRIMINAL MISCHIEF             |63418 |
|BURGLARY OF RESIDENCE         |45327 |
|ASSAULT W/INJURY-FAM/DATE VIOL|42313 |
|HARASSMENT                    |34921 |
|DWI                           |29860 |
|DISTURBANCE - OTHER           |29319 |
|PUBLIC INTOXICATION           |26349 |
|CUSTODY ARREST TRAFFIC WARR   |22042 |
|RUNAWAY CHILD                 |20152 |
|AUTO THEFT                    |19291 |
|ASSAULT WITH INJURY           |19222 |
|BURGLARY NON RESIDENCE        |17587 |
|WARRANT ARREST NON TRAFFIC    |17159 |
|POSSESSION OF MARIJUANA       |15654 |
|POSS OF DRUG PARAPHERNALIA    |14560 |
|POSS CONTROLLED SUB/NARCOTIC  |14095 |
|CRIMINAL TRESPASS             |13725 |
+------------------------------+------+
```

#### Question 4: Which locations are these frequent crimes being committed to?

From this question, we can see where crimes happen the most in Chicago, and from the result, we found out in Chicago most of the crimes occur on the street. The following Spark SQL statement will show the result.

```
top_locations = df_clean.groupBy("location_type").count().orderBy(desc("count")).show(20,False);
```

```
+----------------------------------+------+
|location_type                     |count |
+----------------------------------+------+
|RESIDENCE / HOME                  |508667|
|STREETS / HWY / ROAD / ALLEY      |227327|
|PARKING LOTS / GARAGE             |104159|
|COMMERCIAL / OFFICE BUILDING      |61327 |
|OTHER / UNKNOWN                   |29829 |
|RESTAURANTS                       |12758 |
|HOTEL / MOTEL / ETC.              |12615 |
|BAR / NIGHT CLUB                  |9381  |
|GOVERNMENT / PUBLIC BUILDING      |9314  |
|CONVENIENCE STORE                 |9120  |
|null                              |8776  |
|DEPARTMENT / DISCOUNT STORE       |8091  |
|DRUG STORE / DR. OFFICE / HOSPITAL|5088  |
|SPECIALTY  STORE (TV  FUR ETC.)   |5087  |
|GAS / SERVICE STATIONS            |4764  |
|CONSTRUCTION SITE                 |3973  |
|GROCERY / SUPERMARKET             |3810  |
|BANKS / SAVINGS & LOAN            |3447  |
|SCHOOLS / COLLEGES                |3193  |
|FIELD / WOODS                     |3133  |
+----------------------------------+------+
only showing top 20 rows
```

#### Question 5: Are there specific high crime locations for certain crimes?

From the answer to the question, we can view which location has what type of crimes occurred and view what kind of crimes happen the most in a certain location. The Spark SQL showed the amount of each type of crime for each location.

```
crime_location = df_clean.groupBy("highest_offense_description", "location_type").count().sort("highest_offense_description", "location_type").show(20,False)
```

```
+------------------------------+----------------------------------+-----+
|highest_offense_description   |location_type                     |count|
+------------------------------+----------------------------------+-----+
|ABANDONED REFRIGERATOR        |RESIDENCE / HOME                  |3    |
|ABUSE OF 911                  |DRUG STORE / DR. OFFICE / HOSPITAL|1    |
|ABUSE OF 911                  |HOTEL / MOTEL / ETC.              |1    |
|ABUSE OF 911                  |OTHER / UNKNOWN                   |1    |
|ABUSE OF 911                  |PARKING LOTS / GARAGE             |1    |
|ABUSE OF 911                  |RESIDENCE / HOME                  |19   |
|ABUSE OF 911                  |RESTAURANTS                       |1    |
|ABUSE OF 911                  |STREETS / HWY / ROAD / ALLEY      |3    |
|ABUSE OF CORPSE               |RESIDENCE / HOME                  |1    |
|ABUSE OF OFFICIAL CAPACITY    |null                              |4    |
|ABUSE OF OFFICIAL CAPACITY    |BANKS / SAVINGS & LOAN            |1    |
|ABUSE OF OFFICIAL CAPACITY    |COMMERCIAL / OFFICE BUILDING      |2    |
|ABUSE OF OFFICIAL CAPACITY    |GOVERNMENT / PUBLIC BUILDING      |5    |
|ABUSE OF OFFICIAL CAPACITY    |OTHER / UNKNOWN                   |1    |
|ABUSE OF OFFICIAL CAPACITY    |RESIDENCE / HOME                  |2    |
|AGG ASLT ENHANC STRANGL/SUFFOC|null                              |2    |
|AGG ASLT ENHANC STRANGL/SUFFOC|DRUG STORE / DR. OFFICE / HOSPITAL|1    |
|AGG ASLT ENHANC STRANGL/SUFFOC|FIELD / WOODS                     |3    |
|AGG ASLT ENHANC STRANGL/SUFFOC|HOTEL / MOTEL / ETC.              |20   |
|AGG ASLT ENHANC STRANGL/SUFFOC|OTHER / UNKNOWN                   |2    |
+------------------------------+----------------------------------+-----+
only showing top 20 rows
```

#### Question 6: How has the number of certain crimes changed over the years in Austin?

I wanted to view a specific type of crimes to see if they are increasing or decreasing over the years. I chose Car Burglaries since it is the most occurred crime in Austin, Theft, Possession of Marijuana, and Home Burglaries. The following Spark SQL code will output the amount of each crime per year, respectively.

```
# car burglary
car_theft = df_clean.where("highest_offense_description == 'BURGLARY OF VEHICLE'").groupBy("year").count().orderBy("year").show();

# theft
theft = df_clean.where("highest_offense_description == 'THEFT'").groupBy("year").count().orderBy("year").show();

# possession of marijuana
marijuana = df_clean.where("highest_offense_description == 'POSSESSION OF MARIJUANA'").groupBy("year").count().orderBy("year").show();

# home burglary
home_theft = df_clean.where("highest_offense_description == 'BURGLARY OF RESIDENCE'").groupBy("year").count().orderBy("year").show();
```

```
  car theft        theft        poss. of weed   home robbery
+----+-----+    +----+-----+    +----+-----+    +----+-----+
|year|count|    |year|count|    |year|count|    |year|count|
+----+-----+    +----+-----+    +----+-----+    +----+-----+
|2003| 7567|    |2003| 4367|    |2003|  634|    |2003| 2731|
|2004| 7021|    |2004| 4755|    |2004|  544|    |2004| 2715|
|2005| 7386|    |2005| 4887|    |2005|  531|    |2005| 2781|
|2006| 6702|    |2006| 5262|    |2006|  577|    |2006| 2971|
|2007| 7550|    |2007| 5702|    |2007|  612|    |2007| 3263|
|2008| 6744|    |2008| 5813|    |2008|  684|    |2008| 3215|
|2009| 7974|    |2009| 5614|    |2009| 1237|    |2009| 3847|
|2010| 6696|    |2010| 5608|    |2010| 1458|    |2010| 3903|
|2011| 5943|    |2011| 5011|    |2011| 1310|    |2011| 3048|
|2012| 6171|    |2012| 5054|    |2012| 1229|    |2012| 3136|
|2013| 5840|    |2013| 4799|    |2013| 1216|    |2013| 2714|
|2014| 4973|    |2014| 4419|    |2014| 1148|    |2014| 2419|
|2015| 4542|    |2015| 4307|    |2015| 1001|    |2015| 1951|
|2016| 3946|    |2016| 4137|    |2016|  956|    |2016| 1885|
|2017| 4111|    |2017| 3800|    |2017|  968|    |2017| 1420|
|2018| 4737|    |2018| 3445|    |2018|  830|    |2018| 1338|
|2019| 6096|    |2019| 4036|    |2019|  525|    |2019| 1482|
|2020| 3117|    |2020| 1757|    |2020|  194|    |2020|  508|
+----+-----+    +----+-----+    +----+-----+    +----+-----+
```

## Data Post-Processing:  Visualization

As we got the analysis and result by using Spark SQL in Section 3, we now import the data into Tableau and create charts to get better visualizations and to help understand the resulting data. The following charts are only images, but the Tableau workbook I created is accessible through [here](https://public.tableau.com/profile/lukas7590#!/vizhome/AustinCrimeReports/Sheet1).

#### How has the number of various crimes changed over time in Austin?

We are able to visualize the data as an area-filled bar graph which allows us to visualize which years have the most and least amount of crime. By looking over the chart, we can see that from 2004--2008 there is a steady increase in crime, however, there is almost a 10 year decline in crime. This is then followed by a steep 2 year increase.

![fig1](images/fig1.png)

#### How have the number arrests corresponding to the crimes changed over time in Austin?

As we see in the bar chart, the comparison between the arrest and crimes amount for each year let us see how the police abilities were not quite great since none of the arrests were higher than half of the crime rate. We can conclude from this chart that the security in Austin does not look great with the number of crimes that were not resolved by the arrest.

![fig2](images/fig2.png)

#### Are there any trends in the crimes being committed?

As we can see on the chart, there is quite a large amount of vehicle theft and general theft when compared to other crimes, so when living or visiting Austin it would be best to stay alert for any theft that might occur.

![fig3](images/fig3.png)

#### Which crimes are most frequently committed?

From the pie chart, we can see that vehicle theft has occurred most frequently as it makes up of **14.59%** of crime because it is correlated to how theft could have high return and low risk if not being caught. As we can see in the chart, car theft, family disturbance, theft and criminal mischief are the most frequent occur crimes in Austin which make up of **48.03%** out of all crimes committed.

![fig4](images/fig4.png)

#### Which locations are these frequent crimes being committed to?

From the chart, we can see that most crime is committed in a person's residence.

![fig5](images/fig5.png)

#### Are there certain high crime locations for certain crimes?

From the graph, family disturbances and vechile theft are the top crimes being committed in people's homes. The second top location is any type of street or highway. 

![fig6](images/fig6.png)

### Specific Crime Trends

Lastly, I wanted to look at some of the top crimes being committed in Austin and compare the amount being committed on a yearly basis. 

#### Car Burglary 

The following graph shows the total amount of car theft being committed per year. We can see that the peak was in 2009 with a total of 7,974 occurrences. Since then, there has been a steady decline in occurrences with 2016 as the lowest year by occurrences. However, it seems there has been a rapid increase in the last 3 years.

![fig7](images/fig7.png)

#### Theft

The following graph shows the total amount of general theft being committed per year. We can see that the peak was in 2008 with a total of 5,813 occurrences. Since then, there has been a steady decline in occurrences with 2016 as the lowest year by occurrences.

![fig8](images/fig8.png)

#### Possession of Marijuana

This graph shows the total amount of marijuana possession charges throught the years. This graph is interesting because 2008 was the start of a massive increase in occurrences. More specifically, there is a **113.16%** increase between 2008 and 2010. Since then there seems to be a steady decrease.

![fig9](images/fig9.png)

#### Home Burglary

Lastly, this graph shows the total amount of home theft throughout each year. The peak occurred in 2010 with 3,903 occurrences. 

![fig10](images/fig10.png)

## Conclusion

In this project, I explored Austin Police Department's Crime reports data. This was an interesting project looking at crime trends throught the years. Using Spark SQL provided fast queries when working with this large of a dataset, and Tableau worked well with creating interactive visualizations.

