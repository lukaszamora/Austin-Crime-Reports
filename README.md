# Austin-Crime-Reports

Exploratory data analysis of Austin Police Department's crime reports.

The data was gathered from APD's [Crime Reports](https://data.austintexas.gov/Public-Safety/Crime-Reports/fdj4-gpfu) database. It ranges in crimes committed from 2003 to August 2020. 

From [data.austintexas](https://data.austintexas.gov/Public-Safety/Crime-Reports/fdj4-gpfu):
> This dataset contains a record of incidents that the Austin Police Department responded to and wrote a report. Please note one incident may have several offenses associated with it, but this dataset only depicts the highest level offense of that incident. Data is from 2003 to present. This dataset is updated weekly. Understanding the following conditions will allow you to get the most out of the data provided. Due to the methodological differences in data collection, different data sources may produce different results. This database is updated weekly, and a similar or same search done on different dates can produce different results. Comparisons should not be made between numbers generated with this database to any other official police reports. Data provided represents only calls for police service where a report was written. Totals in the database may vary considerably from official totals following investigation and final categorization. Therefore, the data should not be used for comparisons with Uniform Crime Report statistics.

## Repository Structure

This Github repo has the following structure: 

```
.
├── crime-analysis.sql      # Microsoft SQL Server query file 
├── results                 # Generated .csv files from SQL
├── tableau                 # Tableau notebooks for visualization
└── README.md
```

## Dataset

The dataset `Crime_Reports.csv` contains the following fields:

* `Incident Number`: Incident report number
* `Highest Offense Description`: Description
* `Highest Offense Code`: Code
* `Family Violence`: Incident involves family voilence? `Y`=yes, `N`=no
* `Occurred Date Time`: Date and time (combined) incident occurred
* `Occurred Date`: Date the incident occurred
* `Occurred Time`: Time the incident occurred
* `Report Date Time`: Date and time (combined) incident was reported
* `Report Date`: Date the incident was reported
* `Report Time`: Time the incident was reported
* `Location Type`: General description of the premise where the incident occurred
* `Address`: Incident location
* `Zip Code`: Zip code where incident occurred
* `Council District`: Austin city council district where incident occurred
* `APD Sector`: APD sector where incident occurred
* `APD District`: APD distrcit where incident occurred
* `PRA`: APD police reporting area where incident occurred
* `Cencus Tract`: Cencus tract where incident occurred
* `Clearance Status`: How/whether crime was solved (see lookup)
* `Clearance Date`: Date crime was solved
* `UCR Category`: Code for the most serious crime identified by the FBI as part of its Uniform Crime Reporting program
* `Category Description`: Description for the most serious crimes identified by the FBI as part of its Uniform Crime Reporting program
* `X-coordinate`: X-coordinate where the incident occurred
* `Y-coordinate`: Y-coordinate where the incident occurred
* `Latitude`: Latitude where the incident occurred
* `Longitude`: Longitude where the incident occurred
* `Location`: 3rd party generated spatial column (not from source)
