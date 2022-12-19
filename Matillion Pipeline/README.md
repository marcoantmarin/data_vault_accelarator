# Matillion pipeline


### Description

- Pipeline that automates the creation of Hubs, Links and Satellites.


### Resources

- [Hakkoda’s Matillion instance.](http://matillion-sandbox-linux-vm-01.canadacentral.cloudapp.azure.com/)


### Dependencies

-  ...
-  ...


### Importing the project 

- In order to import the project into your instance follow [this Matillion article](https://documentation.matillion.com/docs/2734441).


# Excecuting the project 
## Data Engineering Lifecycle
The following steps are document the lifecycle of a data engineering pipeline on our project from environment setup, ingesting data into Snowflake, transforming it and finally serving it up for analytics and other downstream uses. 

### 1. Discovery 

Review existing pipelines and ingestion model using Matillion and Fivetran.

### 2. Design Target Data Model

Define the target data model to be developed in Matillion ETL. Use the naming conventions in the appendix for the Curated [Model Layers](#model-layers). Prototype views should be written for each target table for use in unit testing to check row counts and column values (See [Unit Testing](#unit-testing) in the appendix):

### 3. Design Data Pipelines

The design of the Matillion ETL Data Pipelines are documented in the appendix below. This includes Components, pipeline flow recipe and testing. See [Model Organization](#model-organization) for more information.

### 4. Build Data Pipelines

The following steps should be followed for each step of the development process for building a Matillion ETL based data pipeline and following the [Branching Strategy](#branching-strategy) outlined in the appendix:

#### a. Ingest Data

Using a Database Query component to pull the data into our Matillion ETL environment through and JDBC connection. 

#### b. Build Raw Matillion model(s)

Raw data models will be created for each source table/file. use the naming convention below in the [Model Layers](#model-layers) section of the appendix. Raw models should include testing for source freshness and descriptions for the purpose of each table. 

#### c. Build Refined Matillion model(s)

Refined data models will be created to cleanse, standardize and integrate data from Raw Data Models. Use the naming convention below in the [Model Layers](#model-layers) section of the appendix. Refined models should include testing for uniqueness, not null fields and descriptions for the pupose of each model and calculated field. 

#### d. Build Curated Matillion model(s)

Curated data models will be created to prepare data for consumption by analysts and other downstream uses. Use the naming convention below in the [Model Layers](#model-layers) section of the appendix. Curated models should include testing for uniqueness, referential integrity (where applicable), not null fields and descriptions for the purpose of each model and column. 

#### e. Build Matillion job(s)

Matillion Orchestration Jobs will be created to orchestrate Data Warehouse Transformation Jobs, the job(s) will be composed of a series of Matillion ETL components (i.e `SQL Query`, `Calculator`) to load, test and perform maintenance operations. 

#### 5. Deploy Data Pipelines

+ Not sure what to add here just yet. 

## Appendix

### Model organization

| Category | Description                                             |
|----------|---------------------------------------------------------|
| Stage    | Contains models which source data into Matillion ETL    |
|          | from VIRTUSTREAM.                                       |
| Raw      | Contains models which source data from Matillion staging|
|          | area for use downstream in Matillion raw staging area   |
| Information Mart  | Contains models which combine, transform and prepare data for downstream analytics|

Things to note:
- There are different types of models, here we develop a Data Vault 2.0 Data Warehouse Model. 
that typically exist in each of the above categories.  
See [Model Layers](#model-layers) for more information. 

- Read [Building a Data Vault](https://documentation.matillion.com/docs/building-a-data-vault) for an example and more details around building a Data Vault. Note: this article explains Data Vault 1.0, although the logical model is similar, it is highly recommended to take consider the updates the 2.0 version brings. 

- ...
- ...

```
code blocks for commands
```


## Help

For further assistance, feel free to contact *_(add product owner contact)_*


## Authors

- Chris Puuri - chris_puuri@hakkoda.io
- Chris Newgard - chris_newgard@hakkoda.io
- Marco Espinoza- marco_espinoza@Hakkoda.io


## Version History

* 0.2
    * Various bug fixes and optimizations
    * See [commit change]() or See [release history]()
* 0.1
    * Initial Release


## Acknowledgments

Inspiration, code snippets, etc.
*
*
*
