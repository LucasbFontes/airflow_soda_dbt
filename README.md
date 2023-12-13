# Introduction

In this project I will create an end-to-end pipeline using Apache Airflow, Bigquery, DBT, Soda and Metabase. You can check more about it on the [video](https://www.youtube.com/watch?v=DzxtCxi4YaA) that Marc Lambert made explaining the step by step process.

Each technology will have the following importance in this pipeline:

* Airflow: The orchestrate tool, responsible for move the files through each pipeline phase.
* Bigquery: The modern data warehouse, it'll store the information in tables such as the dimension and fact
* DBT: The shelter that will transform the raw data into dimension and fact tables.
* Soda: Used for quality check
* Metabase: The endpoint of our pipeline, will show our data but more in a business vision to analysts and decision makers

Our [source](https://www.kaggle.com/datasets/tunguz/online-retail) will come from kaggle and it's a .csv with sample data.
# Technologies

This section will cover the technologies used in the project, providing a brief overview of each.

## Airflow

Airflow is an open source technologies and one of the most used in the market, is widely used for scheduling, monitoring and developing batch workflows, all of these can be written in Python. Airflow is also known as Workflow as a Code which can be understood as:

* Dynamic: Given that pipelines can be configured as Python code, they allow dynamic pipeline generation;
* Extensible: It contains operators to connect with numerous technologies, hence Airflow can be adjusted to many environments;
* Flexible: The parametrization use the Jinja templating engine.

Also, it has the DAG concept which helps to see/develop the pipeline given that has start and end. Airflow also has an UI that helps to visualize all the DAGs written as well as the historic of each run providing also the logs of it. 

One feature that droves my attention is the option to see your DAG as a graph, this helps a lot when it comes to see if you arranged it as appropriate, plus you can use it to see your execution in real time.

In this project I'll use operators to extract and load data, in this case extract from my local files and load it into Bigquery. Later I will use the cosmos library from Astronomer to transform my data using DBT

## Bigquery

Back in the not too old days(the 2000s) data warehouses were used to store structured data and it served well to the epoch. As years goes by we had a flood of unstructured data and to store only structured data was not enough(after all 80% of the data, nowadays, is unstructured). Enters the Modern Data Warehouses(MDW) that now can support all types of data, and are optimized for noSQL reads/writes.

In this new context, Bigquery (alongside with Databricks, Redshift, Synapse and many more) is classified as one of this types of DW. Also, Bigquery is serveless which means that is not necessary to configure anything, you only pay for the query that you run, denoting that it's highly scalable. 

Bigquery can be divided in several parts. Here are some of the main divisions:

* Project: A project is an organizational and billing unity. Bigquery has to be associated with one.
* Dataset: A logical conteiner that is used to organize and controle the access to the tables.
* Table: Where the data are stored, has rows and columns. Very similar to a relation database table
* Jobs: Bigquery operations, every query can be thought as a job. And every job can be monitored

In this project Bigquery will store our data in tables and we will used the concept of Dimension and Fact tables. The first one is the way to see something that happened, and the second one is this thing that happened. For example: if I buy a cellphone on a ecommerce site, the company that sold it can be see this in various ways:

* Want to see as the marketing team? You'll probably see metrics such as: leads, the time that the buyer spent on the webpage, which path he/she went through.
* Are you from the finance team? So you'll look for the final price, the taxes that you have to pay and you profit on it. 

In the above case the fact is the cellphone sold, and the dimension are the ways you can see the sale(marketing/finance)

Our source will come from Google Cloud Storage, that is - basically - Google datalake.

## Soda

A data quality platform. Nowadays a huge problem we face on the data engineering field, is the quality of the data we delivery to data scientists/data analysts. In fact, it's of no use to send data if it's bad/not valid data. Hence, a new step was added to every pipeline: to check if the data has valid informations. 

Using Soda, this can be done with .yaml files containing the rules that the data must have and providing the answer for the cases.

## DBT

Attends to Data Build Tool and it's one of the hot topics in the field. DBT is a tool that enables analysts and engineer to transform data into their warehouses, it works like a shell to this warehouses, you can modify the data using the DBT UI but the final product will be held into your warehouse(eg. databricks, snowflake, bigquery, synapse, redshift).

DBT is the **T** in the ELT process, hence it's responsible for the final stage of the pipeline. 

Besides using the DBT Cloud its possible to use the CLI, in which users can write code in their editor and then invoke dbt, that'll compile the code and execute against the configured DW. Moreover, it's possible to test your results by writing statements in a .yaml file, this way contributing to solve one of the biggest challenges in the data field nowadays, the quality check.

## Metabase

Metabase is an open source tool focused on providing insights through the creation of dashboards, it's possible to use it on the web without the need to download.  

# The project

This project will showcase an end-to-end pipeline using the above tools in the following way:

 * Airflow will extract a local data 
 * GCS will receivet it
 * Airflow again will move it from GCS to Bigquery
 * Quality check on raw data
 * DBT will transform raw data into accurated data
 * Quality check on accurated data
 * Create report with DBT
 * Quality check on reports
 * Metabase to show the data

In a more visual way, the pipeline will stands as:

<img width="1347" alt="Screenshot 2023-12-09 at 11 59 24" src="https://github.com/LucasbFontes/airflow_soda_dbt/assets/68716835/9551e3ac-eeb3-4970-bf1b-ab596e4faaf5">

## Airflow Operators

Airflow operators will be used to orchestrate all the data movimentation, operators are building blocks of DAG's, they contain the logic of how we are going to process the data, from where we're going to move it and where to put it.

There're different types of operators some more specific than others, also some operators works specific with one technology in this case I used operators to GCS, Bigquery and DBT.

Below it's possible to see an airflow operator example.

![Screenshot 2023-12-09 at 14 35 36](https://github.com/LucasbFontes/airflow_soda_dbt/assets/68716835/56d8c567-1873-4cfb-836d-ebac78bfd3ef)

## Soda checks

In order to validate the data that's going through the pipeline, yaml files were created to check the quality of the data.

<img width="330" alt="Screenshot 2023-12-12 at 21 17 26" src="https://github.com/LucasbFontes/airflow_soda_dbt/assets/68716835/2fadff7d-9bbd-4e0e-acca-25c4b582d52e">

* The schema check will fail in case of customer_id and country columns are missing, and in case this same columns have different types than string.
* Also checks if the column customer_id has duplicates, and print "All customers are unique" when no duplicates found
* Customer_id cannot have missing values.

## DBT tables

  DBT creates tables and put it on bigquery, below we have an example
  
  <img width="727" alt="Screenshot 2023-12-12 at 21 25 30" src="https://github.com/LucasbFontes/airflow_soda_dbt/assets/68716835/39becb40-3ee5-45e8-bf2e-38af4cea5c30">

  * Selects customer_id and country columns from raw_invoices
  * Create a surrogate key based on this columns
  * Filter rows with customer_id null
  * Joined the cte with the country table.

  And since DBT works as a shelter to our Modern DataWarehouse, after the sql runs our bigquery will automatically have the following new tables
  
 <img width="434" alt="Screenshot 2023-12-12 at 21 41 08" src="https://github.com/LucasbFontes/airflow_soda_dbt/assets/68716835/ad7ef1d4-b645-4a6d-9c2e-83d3e56367e7">

## Metabase

The endpoint of our pipeline, after the airflow runs all the reports and validations our dashboard will look like this:

![Screenshot 2023-12-02 at 17 50 25](https://github.com/LucasbFontes/airflow_soda_dbt/assets/68716835/66641d96-909e-4735-9a48-255b5c72a4c7)

