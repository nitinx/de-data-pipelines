# Data Engineering
## Project: Data Pipelines with Airflow

## Project Overview
A music streaming company, Sparkify, has decided that it is time to introduce more automation and monitoring to their data warehouse ETL pipelines and come to the conclusion that the best tool to achieve this is Apache Airflow.

They have decided to bring you into the project and expect you to create high grade data pipelines that are dynamic and built from reusable tasks, can be monitored, and allow easy backfills. They have also noted that the data quality plays a big part when analyses are executed on top the data warehouse and want to run tests against their datasets after the ETL steps have been executed to catch any discrepancies in the datasets.

The source data resides in S3 and needs to be processed in Sparkify's data warehouse in Amazon Redshift. The source datasets consist of JSON logs that tell about user activity in the application and JSON metadata about the songs the users listen to.

### Workflow Requirements

- Stage Loads
	- Dependencies: Nil
	- Independant of each other; can run in parallel.
	
- Fact Load
	- Dependencies: Completion of Stage loads
	
- Dimension Loads
	- Dependencies: Completion of Fact Load
	- Independant of each other; can run in parallel. 

- Dimension Loads
	- Dependencies: Completion of Dimension Loads
	
[![Representation](https://github.com/nitinx/de-data-pipelines/blob/master/dag.png)]

- Other Requirements
	- DAG does not have dependencies on past runs
	- On failure, tasks are to be retried 3 times
	- Retries to happen every 5 minutes
	- Catchup is to be turned off
	- Do not email on retry

### Install

This project requires **Python 3.x** and the following Python libraries installed:

- [Airflow](https://airflow.apache.org/docs/stable/start.html)

### Code

This project contains seven files:

- `dag.py`: Main script with the outline of the DAG.
- `create_tables.sql`: Helper script to create tables on Redshift. 
- `/helpers/sql_queries.py`: Script containing all the SQL queries (INSERTs).
- `/operators/stage_redshift.py`: Operator to source data from S3 and populate stage tables on Redshift.
- `/operators/load_dimension.py`: Operator to load dimensions.
- `/operators/load_fact.py`: Operator to load fact tables.
- `/operators/data_quality.py`: Operator for data quality checks.

### Run

- Start the Airflow web server [`/<home_directory>/airflow/start.sh]
- Once the Airflow UI is available, start the DAG

### Data
Dataset for this project is publicly available on AWS S3: 
- Song Data: s3://udacity-dend/song_data
- Log Data: s3://udacity-dend/log_data

1. **Song Dataset**: Dataset is a subset of real data from the [Million Song Dataset](https://labrosa.ee.columbia.edu/millionsong/). Each file is in JSON format and contains metadata about a song and the artist of that song. The files are partitioned by the first three letters of each song's track ID.
   - `File Count: 385253` / `Total Size: 103 MB`

2. **Log Dataset**: Dataset consists of log files in JSON format generated by this [event simulator](https://github.com/Interana/eventsim) based on the songs in the dataset above. These simulate app activity logs from an imaginary music streaming app based on configuration settings. Log files in the dataset are partitioned by year and month. 
   - `File Count: 31` / `Total Size: 4 MB`

### Schema for Song Play Analysis
Using the song and log datasets, you'll need to create a star schema optimized for queries on song play analysis. This includes the following tables.

#### Fact Table
1. **songplays** - records in log data associated with song plays i.e. records with page NextSong
   - songplay_id, start_time, user_id, level, song_id, artist_id, session_id, location, user_agent

#### Dimension Tables
2. **users** - users in the app
   - user_id, first_name, last_name, gender, level
3. **songs** - songs in music database
   - song_id, title, artist_id, year, duration
4. **artists** - artists in music database
   - artist_id, name, location, lattitude, longitude
5. **time** - timestamps of records in songplays broken down into specific units
   - start_time, hour, day, week, month, year, weekday
