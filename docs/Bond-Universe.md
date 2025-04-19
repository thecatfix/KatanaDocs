# Purpose

[Source Code](https://gitlab.com/katanalabs/katana/-/blob/master/pipelines/generate/components/ingest_bonds_per_universe.py)

The script's primary purpose is to process bond data from IHS Markit, organize it into different categories (like universes or dropped bonds), and export it to various systems for further analysis or storage. It's a comprehensive tool for managing financial data in a structured and automated way.

## Setup and Configuration

The script starts by setting up the necessary environment. It includes importing libraries and configuring the file path. This is like preparing the workspace with all the tools needed for the job.

## Data Processing with Apache Beam

***Classes***  
**IngestBondsPerUniverseOptions:** A class to handle arguments and parameters for the script. It sets up options like run ID, dates, and target tables for exports.

**ReadAllBonds:** A custom data transformation (PTransform in Beam terms) that reads bond prices and instruments, filters them, and converts them to a Bond object.

Export Classes:

**ExportBonds**  
**ExportDroppedBonds**  
**ExportUniverse**

**These are responsible for exporting processed bond data to different destinations like BigQuery and Pub/Sub.**

## Pipeline Execution

The run function is where the main data processing pipeline is defined. It uses the previously defined transformations to process bonds, group them by certain criteria (like ISIN), filter, and then export them. Different kinds of bonds (e.g., dropped bonds, bonds per universe) are handled separately in the pipeline.

Main Function:  
This is where the script execution starts. It sets up the pipeline options, initializes sources and sinks for data (like ihs_prices_source for reading prices, and dropped_bonds_sink for exporting dropped bonds), and then runs the pipeline.
```