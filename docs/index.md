# Welcome To The Katana Wiki


**Katana Diagram Workflow Questions that we need to ask: what is the workflow here**

This is a general step-by-step workflow that outlines how to use Apache Beam with Google Dataflow to transform securities into pairs, covering the stages of ingestion, cleaning, filtering, warehousing, and the Beam pipeline.

The code provides flexible and precise filtering of bonds and application of appropriate models for each universe in downstream processes.

## Generating Pipelines
**Source Code Location**


**Docker Images**
https://console.cloud.google.com/gcr/images/quasar-production/eu/dataflow/templates/flex?project=quasar-production

https://console.cloud.google.com/gcr/images/lens-development?project=lens-development


**Step 1: Ingestion**
1. **Data Source Identification**: Identify the source of your securities data, which could be a database, CSV files, APIs, or real-time streaming data.
2. **Data Ingestion**:
- For batch data, use FileIO to read from CSV or JSON files.
- For streaming data, use PubSub or other streaming sources.

```
  import apache_beam as beam

   # Example for reading from a CSV file
   with beam.Pipeline() as pipeline:
       securities_data = (
           pipeline
           | 'ReadFromCSV' >> beam.io.ReadFromText('gs://your-bucket/securities.csv', skip_header_lines=1)
       )
```

**Step 2: Cleaning**
3. **Data Cleaning**:
- Remove any leading/trailing whitespace.
- Handle missing values (e.g., remove records with null values).
- Normalize data (e.g., standardize formats).
```
  def clean_data(record):
       # Implement cleaning logic here
       return cleaned_record

   cleaned_data = (
       securities_data
       | 'CleanData' >> beam.Map(clean_data)
   )
```
**Step 3: Filtering**
4. **Data Filtering**:
- Filter out unwanted records based on specific criteria (e.g., only include securities above a certain market cap).
```
  def filter_securities(record):
       # Implement filtering logic here
       return is_valid_record

   filtered_data = (
       cleaned_data
       | 'FilterSecurities' >> beam.Filter(filter_securities)
   )
```
**Step 4: Pairing**
5. **Pairing Securities**:
- Transform the filtered data into pairs based on your criteria (e.g., pairing by sector, market cap, etc.).

```
  def pair_securities(security_list):
       # Implement pairing logic here
       return pairs

   paired_data = (
       filtered_data
       | 'PairSecurities' >> beam.Map(pair_securities)
   )
```

**Step 5: Warehousing**
**Data Warehousing**:
- Write the transformed data to a data warehouse or storage solution (e.g., BigQuery, Cloud Storage).

```
  paired_data | 'WriteToBigQuery' >> beam.io.WriteToBigQuery(
       'your_project_id:your_dataset.your_table',
       schema='SCHEMA_AUTODETECT',
       write_disposition=beam.io.BigQueryDisposition.WRITE_APPEND
   )

```
**Step 6: Run the Pipeline**
7. **Run the Dataflow Pipeline**:
- Execute the pipeline on Google Cloud Dataflow.
```

  options = beam.options.pipeline_options.GoogleCloudOptions(
       project='your_project_id',
       job_name='securities-pairing',
       temp_location='gs://your-bucket/temp/',
       region='your-region'
   )
   pipeline_options = beam.options.pipeline_options.PipelineOptions(flags=[], **options)

   with beam.Pipeline(options=pipeline_options) as pipeline:
       # Include all previous steps here
```


Summary
This workflow outlines the steps to ingest, clean, filter, pair, and warehouse securities data using Apache Beam and Google Dataflow. You can customize the cleaning, filtering, and pairing logic according to your specific requirements. Make sure to handle exceptions and logging for better monitoring of your pipeline execution.
