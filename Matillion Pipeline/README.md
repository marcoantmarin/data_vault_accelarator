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
| Stage    | Contains models which source data into Matillion ETL from VIRTUSTREAM.|
| Raw      | Contains models which source data from Matillion staging area for use downstream in Matillion raw staging area|
| Information Mart  | Contains models which combine, transform and prepare data for downstream analytics|

Things to note:
- There are different types of models, here we develop a Data Vault 2.0 Data Warehouse Model. 
that typically exist in each of the above categories.  
See [Model Layers](#model-layers) for more information. 

- Read [Building a Data Vault](https://documentation.matillion.com/docs/building-a-data-vault) for an example and more details around building a Data Vault. Note: this article explains Data Vault 1.0, although the logical model is similar, it is highly recommended to take consider the updates the 2.0 version brings. 

- The following are the DAG stages that we tend to utilize:
  <details>


  <summary>Common</summary>

    | dag_stage | Typically found in | description                                                        |
    |-----------|--------------------|--------------------------------------------------------------------|
    | stg_     | /models/stg             | <li> Indicates a data set created from dbt-external-tables macros. |
    | raw_      | /models/raw    | <li> Indicates a data set that is being sourced into dbt. </li><li> This layer is typically virtual and it represents a 1:1 relationship between the source from the Snowflake staging database and first layer of models. </li> |                                                                                                           |
    | int_      | /models/refined      | <li> Indicates a logical step towards creating a final data set. </li><li>Typically used for:</li><ul><li>Breaking up models that will feed fct_ or dim_ models into smaller pieces to reduce complexity</li><li>Creating a reusable data set to reference in multiple downstream fct_ and dim_ models</li></ul> |
    | base_     | /models/refined    | <li> Indicates cleaning, flattening and standardization on a data set before joining to other data sets in `int_`  models. </li> |  
    | dim_      | /models/curated      | <li> Flags data which is used to describe an entity. </li><li> Indicates a final data which is robust, versatile, and ready for consumption. </li> |
    | fct_      | /models/curated      | <li> Flags data which is in the form of numeric facts observed during measurement events. </li><li> Indicates a final data which is robust, versatile, and ready for consumption. </li> |
  
  </details>

### Model File Naming and Coding

- All objects should be plural.  
  Example: `HUB_CENTER` vs. `SAT_CENTER`

- Schema, table and column names should be in `snake_case` (i.e. an underscore separating words).

- Limit use of abbreviations that are related to domain knowledge. An analyst will understand `medical_record_number` better than `mrn`.

- Use names based on the _business_ terminology, rather than the source terminology.

- Each model should have a primary key that can identify the unique row, and should be named `<object>_id`, e.g. `account_id` – this makes it easier to know what `id` is being referenced in downstream joined models.

- If a surrogate (hash) key is created, it should be named `HK_NAME_ID`.

- Avoid using reserved words (such as [these](https://docs.snowflake.com/en/sql-reference/reserved-keywords.html) for Snowflake) as column names.

- Consistency is key! Use the same field names across models where possible.  
Example: a key to the `participant` table should be named `participant_id` rather than `user_id`.

Model configurations at the folder level should be considered (and if applicable, applied) fist. For this case, defining a project within the Matillion ETL instance, which is called DataPlatform_VirtuStreamClarity. Once we set this project, within the default folder there will be three folders and one *_orchestration job_* these are defined as follow:


| Stage    | Description                                             |
|----------|---------------------------------------------------------|
| RAW_STAGE| Folder containing raw objects that come from source     |
| RAW_VAULT| Folder contining standardize objects like hubs and satellites|
| Information Mart| Folder containing objects ready for downstream analytics|
| BUILD_DATA_VAULT|Orchestration job containing all preview objects ready to be run in parallel|

### Unit testing 

At a minimum, developers need to perform the following unit tests before creating their Pull Request. Screenshots of the successful unit tests should be included as part of your pull request in the [Validation of models](https://github.com/Hakkoda1/dbt_capstone_solution/blob/main/pull_request_template.md#validation-of-models) section.

  1. [Compare Row Counts](https://github.com/dbt-labs/dbt-audit-helper/tree/0.6.0/#compare_relations-source) 
  This test will ensure that all rows have arrived in the target when compared to the source. Add a screenshot of the unit test preview from the Snowflake in your pull request similar to the table below:

      | Business Key | Hash Key | Record Source | Load_datetime |
      |----|----|----|----|
      | Query produced no results |

using the following query:

```sql
SELECT *
FROM HUB_NAME hub
LEFT OUTER JOIN SAT_NAME sat
ON hub.HK_NAME_ID = sat.HK_NAME_ID
WHERE sat.HK_NAME_ID IS NULL; 
```

+ Not sure if we will add this:
  2. [Compare Column data types](https://github.com/dbt-labs/dbt-audit-helper/tree/0.6.0/#compare_all_columns-source)
  This test will ensure that all data types & ordinal positions align between the source and target.  Add a screenshot of the unit test preview from the dbt Cloud UI in your pull request similar to the table below:

      | column_name	| a_ordinal_position	| b_ordinal_position	| a_data_type |	b_data_type |
      |-----------|-----------|-----------|-----------|-----------|
      |patient_skey	| 1	| 1	| binary	| binary |
      |patient_id	| 2	| 2	| varchar	| varchar |
      |birth_date	| 3	| 3	| timestamp	| date | 

## Branching Strategy
Our pull requests (typically) fit into four main categories:

| Category | Description                                             |
|----------|---------------------------------------------------------|
| Dev      |           |
| Prod     |           |
| Test     |           |
| feature  |    |

The following diagram highlights the branching strategy defined for the project:

![image](https://drive.google.com/uc?export=view&id=1F5iwwkFwbJ1fO1LwOnlUn69_C9FS2VRk)

### Deployment Workflow 
1. Developer creates a branch from Dev and names it feature, fix or test and makes their changes or additions.
2. Developer unit tests their changes or additions against a clone of PROD (or QA)
3. A PR is created by the developer from their feature/fix/test branch to QA branch
4. PR is automatically labeled feature, fix or test based on the naming convention of “feature/logical-feature-name” by GitHub PR Labeler action.
5. PR is reviewed and merged into QA branch by an admin
6. QA job is run and QA/UAT is performed on the change/addition 
7. Once ready for production, an admin uses Github  action ‘Draft a new release’ to trigger creation of a release branch PR to the PROD branch.
8. After the release branch PR is merged a second Github action triggers creation of  a git Release and generates release notes.

### Pull Request Template
[This pull request template]([pull_request_template.md](https://github.com/Hakkoda1/dbt_capstone_solution/blob/main/pull_request_template.md#validation-of-models)), located in the root of our repository, has been created to standardize pull requests across the project team. For reference, review [Git Integration with Matillion ETL](https://documentation.matillion.com/docs/en/2974180) describes the architecture of Matillion ETL's Git integration feature and documents actions including commit, create branch, merge, push, fetch, and more. 
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
