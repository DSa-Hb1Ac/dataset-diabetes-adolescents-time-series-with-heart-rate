This repository contains a curated dataset comprising real patient data (24 adolescents with type 1 diabetes) from a insulin pump, continuous/flash blood glucose monitor (CGM/FGM), activity tracker and carbohydrates estimates over a period of 3 months.

**Note**: [Git LFS](https://git-lfs.github.com/) is required to clone the CSV files in [data-csv/Fitbit/](data-csv/Fitbit)

# Data pre-processing

This section described per device/system how the data was obtained and pre-processed. <br>
Refer to the [data format section](#data-format) for a detailed description of the data format.

- Personally identifiable data was removed.
- In some cases the export files contained data before the start (2019-10-15) and after the end (2020-01-11) of the data gathering period, this data was removed. 
 
### General patient information
- Obtained via the hospital in an Excel file (.xlsx) which was extracted from the Hospital Information System. 

### Medtronic MiniMed 640G and MiniMed 670G (Insulin pumps)
- Export files (in CSV format) per subject were obtained via the hospital.
- For some subjects two export files ('part 1' and 'part 2') were obtained. In some cases overlap occurred between these files, this duplicate data was removed.
- For subjects with an Medtronic CGM (Enlite?), the blood glucose measurements are also in this file
- The different export files were combined into one CSV file and the column '_Subject code number_'  was added. 

### Abbott FreeStyle Libre (Flash Glucose Monitor)
- Export files (in CSV format) per subject were obtained via the hospital. 
- In some cases duplicate data was present within an export file (possible from both a Freestyle Libre Reader and the LibreLink app?), this data was removed.
- The different export files were combined into one CSV file and the column '_Subject code number_'  was added. 

### Fitbit Inspire HR (Activity tracker)
- Data was obtained via the [Fitbit Web API](https://dev.fitbit.com/build/reference/web-api/) in json format and saved in a file per subject per day.
- The different export files (in json format) were combined into one CSV file and the column '_Subject code number_' was added. 


## Date and time information

The formatting of date and time information in the original exports was not equal across the different export files. Date and time information was equalized to a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) time stamp in the format _YYYY-MM-DDThh:mm:ss±hhmm_. For convenience the date, time and UTC offset were also added as separate columns.

## Time zone information

All timing information of the original data sources were in local time and lacked time zone information. Time zone information was added automatically after obtaining the export files, assuming all data was collected in the Europe/Amsterdam timezone.

### Daylight Saving Time (DST)
The period in which the data was collected contains a transition from Central European Summer Time (CEST) to Central European Time (CET): one second after 2019-10-27T02:59:59+0200 the time is 2019-10-27T02:00:00+0100. Each device/system handles this transition differently:

**Medtronic MiniMed 640G and MiniMed 670G** <br>
The clock of this devices doesn't adjust automatically when a transition from CEST to CET occurs; the device clock has to be adjusted manually. Adjustment of the clock can be found in the export file in column 8 - '_New Device Time_'. In the current version of the data, time zone offsets have been added automatically based on the local clock. Timestamps labelled as 2019-10-27T03:00:00+0100 to the moment the user adjusted the device clock manually (indicated by an entry in column 8 - '_New Device Time_'), are invalid, i.e. one hour needs to be subtracted from the local clock time indicated. This manual correction has **NOT** been done in the current version of the data.

**Abbott FreeStyle Libre** <br>
Shortly after 2019-10-27T02:59:59+0200, the clock of the FreeStyle Libre (_sensor or reader/app?_) adjusts itself automatically. The data measured from 2019-10-27T02:00:00+0200 to 2019-10-27T02:59:59+0200 and 2019-10-27T02:00:00+0100 to 2019-10-27T02:59:59+0100 appeared in the export file without time zone information. Therefore, we omitted the time zone information for the data records from 2019-10-27T02:00 to 2019-10-27T02:59.

**Fitbit Inspire HR** <br>
Shortly after 2019-10-27T02:59:59+0200, the clock of the Fitbit Inspire HR adjusts itself automatically. The data collected between 2019-10-27T02:00:00+0200 and 2019-10-27T02:59:59+0200 is overwritten with the data from 2019-10-27T02:00:00+0100 to 2019-10-27T02:59:59+0100 (therefore 2019-10-27 has 24 hours of data instead of 25 hours).

# Data format

In this section the data format is described.<br>

## Population 

**source:** [data-csv/population.csv](data-csv/population.csv) <br>

| Column index | Column name |  Description | Unit | Format | Example |
|---|---|---|---|---|---|
|1| **Subject code number** | Number indicating which subject the data record belongs to  || numeric (integer) | 903 |
|2| **Gender [M=male F=female]** | Gender of the subject [M=male, F=female] || character | F |
|3| **Length [cm]** | Length of subject | centimeter | numeric (floating point) | 168.6 |
|4| **Weight [kg]** | Weight of subject | kilogram |numeric (floating point) | 58.3 |
|5| **Age [yr]** | Age of subject in years | |numeric (integer) | 14 |
|6| **HbA1C_1 [yyyy]** | Year in which the first HbA1C (glycated hemoglobin) measurement was taken | |YYYY ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)) | 2019 |
|7| **HbA1C_1 [mm]** | Month in which the first HbA1C (glycated hemoglobin) measurement was taken| |numeric (integer) | 09 |
|8| **HbA1C_1 [mmol/mol]** | First HbA1C (glycated hemoglobin) measurement |millimol per mol | numeric (integer) | 54 |
|9| **HbA1C_2 [yyyy]** | Year in which the second HbA1C (glycated hemoglobin) measurement was taken | |YYYY ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))  | 2020 |
|10| **HbA1C_2 [mm]** | Month in which the second HbA1C (glycated hemoglobin) measurement was taken | |numeric (integer) | 01 |
|11| **HbA1C_2 [mmol/mol]** | Second HbA1C (glycated hemoglobin) measurement | millimol per mol | numeric (integer) | 53 |
|12| **Pump brand** | Brand of the insulin pump used by the subject | |character | Medtronic |
|13| **Pump model** | Model of the insulin pump used by the subject | |character | MiniMed 640G |
|14| **FGM/CGM brand** | Brand of the Flash Glucose Monitor (FGM) or Continuous Glucose Monitor (CGM) used by the subject | |character | Abbott |
|15| **FGM/CGM model** | Model of the Flash Glucose Monitor (FGM) or Continuous Glucose Monitor (CGM) used by the subject | |character | Freestyle Libre |
|16| **Activity tracker brand** | Brand of activity tracker used by the subject | |character | Fitbit |
|17| **Activity tracker model** | Model of activity tracker used by the subject | |character | Inspire HR |


## Insulin Pump 

**source:** [data-csv/Medtronic.csv](data-csv/Medtronic.csv) <br>

**Note:**
- Only the columns used in our research are described here.
- For subjects with an Abbot Freestyle Libre FGM the blood glucose data can be found in [data-csv/Abbott.csv](data-csv/Abbott.csv)
- Not all subjects are present in this data source nor does it contain always data of a 3 month period for each subject.
- Subject 912 and 929 switched from the MiniMed 640G to the MiniMed 670G during the study. The first file is from the MiniMed 640G, the second from the MiniMed 670G.

| Column index | Column name |  Description | Unit | Format | Example |
|---|---|---|---|---|---|
|1| **Subject code number** | Number indicating which subject the data record belongs to || numeric (integer) | 903 |
|2| **Local datetime [ISO8601]** | Date and time in local time of the data record <br> **Note:** Data records per subject are sorted on 'File' and 'Index' and therefore do not necessarily appear in chronological order in this data source ||YYYY-MM-DDThh:mm:ss±hhmm ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))  | 2019-10-15T12:00:00+0200 |
|3| **Local date [yyyy-mm-dd]** | Date in local time of the data record || YYYY-MM-DD ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))  | 2019-10-15 |
|4| **Local time [hh:mm]** | Time in local time of the data record || hh:mm ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))  | 12:00 |
|5| **UTC offset [hr]** | Difference in hours for the local time from Coordinated Universal Time (UTC)  | hour | numeric (floating point) | 2 |
|6| **File** | File number of pump export files <br>_(some pump exports were supplied in multiple files. Original sequence of events can be reconstructed with this column and column 7 -  'Index')_|  | numeric (integer) | 1 |
|7| **Index** | Original Index number of pump export file  |  | numeric (integer) | 21 |
|9| **BG Reading [mmol/l]**|Manual blood glucose reading from a (connected) Blood Glucose Meter (BGM)|millimol per liter|numeric (floating point) |5.6|
|10| **Basal Rate [IU/hr]**|Current [basal rate](https://en.wikipedia.org/wiki/Basal_rate) of the insulin pump|[International Unit](https://en.wikipedia.org/wiki/International_unit) per hour|numeric (floating point) |0.2|
|11| **Temp Basal Amount**|Temporary increase or decrease amount of the basal rate| _depends on column 12_ | _depends on column 12_ | _depends on column 12_ |
|12| **Temp Basal Type**|Temporary basal rate type of column 11 | | character  | Percent |
|13| **Temp Basal Duration [hh:mm:ss]**|Duration of the temporary increase or decrease of the basal rate|  | hh:mm:ss  | 00:42:00 |
|14| **Bolus Type**|Type of [bolus](https://en.wikipedia.org/wiki/Bolus_(medicine)) given by insulin pump <br>_Normal / Square / Dual (combination of Normal and Square), [more information on bolus types](https://s3.amazonaws.com/medtronic-hcp/Dual-Square%20Quick%20Reference%20Guide%20for%20MiniMed%20530G.pdf)_|  | character | Normal |
|16| **Bolus Volume Delivered [IU]**|Amount of Insulin administered by the insulin pump for this bolus|[International Unit](https://en.wikipedia.org/wiki/International_unit)|numeric (floating point) |1.8|
|17| **Bolus Duration [hh:mm:ss]**|Duration of the bolus (for Square and Dual wave (square part) type)| | hh:mm:ss | 01:30:00 |
|23| **BWZ Estimate [IU]** | Bolus Wizard (BWZ) output: Estimate of amount of insulin to deliver of the dosage calculation by the Bolus Wizard (BWZ)|[International Unit](https://en.wikipedia.org/wiki/International_unit)|numeric (floating point) | 0.8 | 
|24| **BWZ Target High BG [mmol/l]** | Bolus Wizard (BWZ) input: High value of the blood glucose target to reach |millimol per liter |numeric (floating point) | 6.0 | 
|25| **BWZ Target Low BG [mmol/l]** | Bolus Wizard (BWZ) input: Low value of the blood glucose target to reach |millimol per liter |numeric (floating point) | 5.5 | 
|26| **BWZ Carb Ratio [g/IU]** | Bolus Wizard (BWZ) input: insulin to carbohydrate ratio (ICR) |grams per [International Unit](https://en.wikipedia.org/wiki/International_unit) |numeric (integer) | 24 | 
|27| **BWZ Insulin Sensitivity [mmol/L/U]** | Bolus Wizard (BWZ) input: insulin sensitivity factor (ISF) |millimol per liter per [International Unit](https://en.wikipedia.org/wiki/International_unit) |numeric (integer) | 6 | 
|28| **BWZ Carb Input [g]**|Bolus Wizard (BWZ) input: Amount of carbohydrates |grams|numeric (integer) |35|
|29| **BWZ BG Input [mmol/l]**|Bolus Wizard (BWZ) input: current blood glucose value |millimol per liter |numeric (floating point)|8.3|
|30| **BWZ Correction Estimate [IU]**|Bolus Wizard (BWZ) output: 'food-part' of insulin dosage calculation |[International Unit](https://en.wikipedia.org/wiki/International_unit)|numeric (floating point)|0.4|
|31| **BWZ Food Estimate [IU]**|Bolus Wizard (BWZ) output: 'correction-part' of insulin dosage calculation |[International Unit](https://en.wikipedia.org/wiki/International_unit)|numeric (floating point)|0.4|
|32| **BWZ Active Insulin [IU]**|Bolus Wizard (BWZ) input: insulin on board (IOB) |[International Unit](https://en.wikipedia.org/wiki/International_unit)|numeric (floating point)|0.2|
|34| **Sensor Glucose [mmol/l]**| Automatic blood glucose reading from a Flash Glucose Monitor (FGM) or Continuous Glucose Monitor (CGM) |millimol per liter|numeric (floating point)|8.3|


## Blood glucose

**source:** [data-csv/Abbott.csv](data-csv/Abbott.csv) <br>

**Note:** 
- Only the columns used in our research are described here.
- For subjects with a Medtronic FGM/CGM the blood glucose data can be found in [data-csv/Medtronic.csv](data-csv/Medtronic.csv)
- Not all subjects are present in this data source nor does it contain always data of a 3 month period for each subject.

| Column index | Column name |  Description | Unit | Format | Example |
|---|---|---|---|---|---|
|1| **Subject code number** | Number indicating which subject the data record belongs to || numeric (integer) | 903 |
|2| **Local datetime [ISO8601]** | Date and time in local time of the data record <br> ||YYYY-MM-DDThh:mm:ss±hhmm ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))  | 2019-10-15T12:00:00+0200 |
|3| **Local date [yyyy-mm-dd]** | Date in local timeof the data record || YYYY-MM-DD ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))  | 2019-10-15 |
|4| **Local time [hh:mm]** | Time in local time of the data record || hh:mm ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))  | 12:00 |
|5| **UTC offset [hr]** | Difference in hours for the local time from Coordinated Universal Time (UTC)  | hour | numeric (floating point) | 2 |
|6|**Record Type**|Type of the data record: <br> 0 = Historic Glucose <br>1 = Scan Glucose <br> 2 = Strip Glucose <br> 3 = ? (not present in this data source) <br> 4 = Non-numeric Rapid-Acting Insulin <br> 5 = Non-numeric Food<br> 6 = ? (possibly indicates a (NFC?) reading error)||numeric (integer)|0|
|7|**Historic Glucose [mmol/l]**| Historic automatic glucose reading from the sensor (every 15 minutes) <br> **Note:** A maximum of 8 hours is stored on the FreeStyle Libre sensor|millimol per liter|numeric (floating point)|5.7|
|8|**Scan Glucose [mmol/l]**| Manual glucose reading from the FreeStyle Libre sensor. <br>Occurs when the subject scans the FreeStyle Libre sensor with a FreeStyle Libre Reader or smartphone.<br> Historic glucose readings up to 8 hours or the previous manual reading are also transferred at this point. |millimol per liter|numeric (floating point)|6.3|


## Heart rate

**source:** [data-csv/Fitbit/Fitbit-heart-rate.csv](data-csv/Fitbit/Fitbit-heart-rate.csv) <br>

| Column index | Column name |  Description | Unit | Format | Example |
|---|---|---|---|---|---|
|1| **Subject code number** | Number indicating which subject the data record belongs to || numeric (integer) | 903 |
|2| **Local datetime [ISO8601]** | Date and time in local time (Central European Time (CET)) of the data record ||YYYY-MM-DDThh:mm:ss±hhmm ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))  | 2019-10-15T12:00:00+0200 |
|3| **Local date [yyyy-mm-dd]** | Date in local time of the data record || YYYY-MM-DD ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))  | 2019-10-15 |
|4| **Local time [hh:mm]** | Time in local time of the data record || hh:mm ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))  | 12:00 |
|5| **UTC offset [hr]** | Difference in hours for the local time (Central European Time (CET)) from Coordinated Universal Time (UTC)  | hour | numeric (floating point) | 2 |
|6| **heart rate [#/min]** | Heart rate of the subject | beats per minute | numeric (integer) | 142 |


## Steps

**source:** [data-csv/Fitbit/Fitbit-steps.csv](data-csv/Fitbit/Fitbit-steps.csv) <br>

| Column index | Column name |  Description | Unit | Format | Example |
|---|---|---|---|---|---|
|1| **Subject code number** | Number indicating which subject the data record belongs to || numeric (integer) | 903 |
|2| **Local datetime [ISO8601]** | Date and time in local time of the data record ||YYYY-MM-DDThh:mm:ss±hhmm ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))  | 2019-10-15T12:00:00+0200 |
|3| **Local date [yyyy-mm-dd]** | Date in local time of the data record || YYYY-MM-DD ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))  | 2019-10-15 |
|4| **Local time [hh:mm]** | Time in local time of the data record || hh:mm ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))  | 12:00 |
|5| **UTC offset [hr]** | Difference in hours for the local time from Coordinated Universal Time (UTC)  | hour | numeric (floating point) | 2 |
|6| **steps [#]** | number of steps taken in this minute by the subject <br> **note:** The [Fitbit Web API](https://dev.fitbit.com/build/reference/web-api/) returns 0 for both 0 steps and no measurement |  | numeric (integer) | 42 |


## Sleep

**source:** [data-csv/Fitbit/Fitbit-sleep.csv](data-csv/Fitbit/Fitbit-sleep.csv) <br>

| Column index | Column name |  Description | Unit | Format | Example |
|---|---|---|---|---|---|
|1| **Subject code number** | Number indicating which subject the data record belongs to || numeric (integer) | 903 |
|2| **Local datetime start [ISO8601]** | Start date and time in local time of the sleep level record ||YYYY-MM-DDThh:mm:ss±hhmm ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))  | 2019-10-15T12:00:00+0200 |
|3| **Local date start [yyyy-mm-dd]** | Start date in local time of the sleep level record|| YYYY-MM-DD ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))  | 2019-10-15 |
|4| **Local time start [hh:mm]** | Start time in local time of the sleep level record || hh:mm ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))  | 12:00 |
|5| **UTC offset start [hr]** | Difference in hours for the local start time from Coordinated Universal Time (UTC)  | hour | numeric (floating point) | 2 |
|6| **level** | Level of the sleep. <br> Refer to the [Fitbit Web API](https://dev.fitbit.com/build/reference/web-api/sleep/) for more information on the different sleep levels || character | deep |
|7| **duration [s]** | Duration of the sleep | seconds | numeric (integer) | 4200 |
