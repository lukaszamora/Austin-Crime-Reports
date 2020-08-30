# Austin-Crime-Reports

Exploratory data analysis of Austin Police Department's crime reports.

The data was gathered from APD's [Crime Reports](https://data.austintexas.gov/Public-Safety/Crime-Reports/fdj4-gpfu) database. It ranges in crimes committed from 2003 to August 2020. 

From [data.austintexas](https://data.austintexas.gov/Public-Safety/Crime-Reports/fdj4-gpfu):
> This dataset contains a record of incidents that the Austin Police Department responded to and wrote a report. Please note one incident may have several offenses associated with it, but this dataset only depicts the highest level offense of that incident. Data is from 2003 to present. This dataset is updated weekly. Understanding the following conditions will allow you to get the most out of the data provided. Due to the methodological differences in data collection, different data sources may produce different results. This database is updated weekly, and a similar or same search done on different dates can produce different results. Comparisons should not be made between numbers generated with this database to any other official police reports. Data provided represents only calls for police service where a report was written. Totals in the database may vary considerably from official totals following investigation and final categorization. Therefore, the data should not be used for comparisons with Uniform Crime Report statistics.

## Introduction/Problem Definition

To have a perspective of the state of security of Austin I defined few questions, which I answered during this data analytic project. Here is the list of these questions:
1. How has the number of various crimes changed over time in Austin?
2. How have the number arrests corresponded to the crimes changed over time in Austin?
3. Which crimes are most frequently committed?
4. Which locations are these frequent crimes being committed to?
5. Are there certain high crime locations for certain crimes?
6. How has the number of certain crimes changed over the years in Austin?

To answer these question I took the four main steps of the KDD data mining pipeline, which are respectively, data preprocessing, data pre-processing, analysis and post-processing. In this documentation, I also use the same name for each section of the report. In Section 2, I describe how I gathered our data and the tasks I did in regard to clean the data as the data Pre-Processing phase. Section 3 I dive straight into the data analysis process, firstly, introducing the methods and technologies I used and then provide details on how I dealt with crime data in Spark SQL. Section 4 dives into the visualization of my results. Finally, in Section 5 I bring the conclusion.
