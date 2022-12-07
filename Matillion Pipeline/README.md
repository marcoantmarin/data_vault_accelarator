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


### Excecuting the project 

# Static pipeline

## Main Orchestration job 

+ This piece is part of the static model created for Data Vault 2.0, it consists of a main orchestration job and the two stages designed for Data Vault 2.0
+ consisting of the Raw Stage and the Raw Vault, plus the Information Mart component. 
  
### Raw Stage

+ The Raw Stage is made up of 3 transformation jobs for each component, it aims to create a table with new inserts containing both descriptive data and the Data Vault columns such as Hashing keys. 

#### Components recipe

For the creation of most of the components in Matillion, the following were used:
1. Table input
This component calls the target table, can be a Hub or Satellite. 
2. Calculation component
There are two components for calculation, first one creates the hashing and the other one adds constant values such as record source and most importantly the collision key. 
3. SQL component
This component calls the source table from Vistustream.
1. Join
Left outer join to join both table
1. Filter
Filter component to check for null hash keys
1. Table output/append
append new records. 


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
