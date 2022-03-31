# Data Quality Assessment Tool

The scripts included in this repository can be used to assess the quality of a datasets by computing various data quality metrics. Currently, the focus is on temporal data that may include the data generated by IoT sensors such as Air quality monitoring (AQM) sensors, Vehicle tracking sensors etc.

For each quality metric that is used to quantify the quality of a dataset, the scripts aims to provide a metric score between 0 and 1, where 1 is the highest possible score, indicating a 100% score.
The tool is able to assess the following metrics:
- Format Adherence
- Data Completeness
- Additional Attribute Presence
- Regularity of Arrivals
- Presence of duplicates in the data

NOTE: It is expected that the data quality assesser provide a schema for the dataset that is being evaluated. In our case, the schemas are provided in JSON-schema format and the appropriate links are populated via the input configuration files. In particular, the JSON-Schema file defines the syntactic structure of a data packet along with information about mandatory attributes.

### Format Adherence

This metric assesses the level of adherence of the data to its expected data fromats. It computes the fraction of packets adhering to the expected schemas. The output is a score between 0 and 1, where 1 is the highest possible score, indicating that the dataset is 100% adherent to the format.

### Field Completeness

This metric assesses the level of presence of mandatory attributes in the dataset. For each mandatory attribute it computes the fraction of packets that contain this attribute and the final metric is computed by averaging over all the mandatory attributes. The output is a score between 0 and 1, where 1 is the highest possible score, indicating that the dataset is 100% complete. NOTE that a 'null' value of an attribute is treated as not present.

### Additional Attribute Presence

This metric assesses the level of absence(or presence) of 'unknown' attributes in the data. An attribute is treated as unknown if the information about it is not present in the schema file. The output is a score between 0 and 1, where 1 is the highest possible score, indicating that there are no additional attributes present.

### Duplicate Presence
This metric assesses the level of duplicate rows in the dataset. A value/row/packet is considered to be a duplicate if the 'specified' set of attribtues/columns contain the exact same values in any other data packet. The set of columns used in this computation is specified by the data-quality assesser via the input configuration file. It is important to choose the appropriate combination of columns to correctly find values that are duplicates. The output is a score between 0 and 1, where 1 is the highest possible score, indicating that there are no duplicates present in the dataset.

### Regularity of Data Packet Arrivals

This metric assesses the regularity of arrivals of data for a temporal data source. This metric analyses the historgram of interarrival times of data for a given stream and computes the fraction of packets that lie within (1+/- alpha)*Mode. Here, alpha is a user-defined parameter specified via the configuration file. An appropriate set of values for alpha will enable the user to understand the spread of the inter-arrival times from the mode. The output score, which is between 0 to 1, 1 being the highest possible score indicating all the inter-arrival times are within [(1-alpha)*Mode, (1+alpha)*Mode]. Along with the metric score, the script also outputs a plot of the time deltas in a '.pdf' format, as well as the the mean, standard deviation, and mode of the time deltas of any dataset.

## How to Run the Tool
Clone the repo from:

``` console
git clone https://github.com/datakaveri/data-quality-assessment
```
This will create the directory structure locally on your system.

### Required libraries and packages
From the *scripts* folder, run the following command to install the package and library dependencies:

```console
pip install -r requirements.txt
```

Present in the *config* folder is a config file in *JSON* format with the name of the dataset prepended to it. This file requires one to input the name of the datafile as well as select the attributes that one would like to check for duplicates. In this case, the name of the datafile and the appropriate attributes for selection are already included in the file as below: 
- *observationDateTime*
- *id* for AQM data & *trip_id* for ITMS data

Present in the *data* folder in the repository is a sample dataset of ITMS data from Surat, as well as a sample dataset of AQM data from Pune. Inside the *schemas* folder are the corresponding schemas for these datasets. 
In order to run the scripts that assess the data quality, make sure your current directory is the *scripts* folder. The scripts can then be run as below with included system arguments. Note that the system argument is the config file name only and not the relative path of the file.

```console
python3 DuplicationDetection.py <config file name>
```
```console
python3 InterArrivalTime.py <config file name>
```
```console
python3 FormatValidation.py <config file name>
```

Ensure that the datasets are in *.csv* and *JSON* format and are located in the *data* folder.

The output report file will be generated in a *JSON* format and and a *.pdf* format and will be saved in the outputReports folder. All the scripts will append their results to the same output file.
