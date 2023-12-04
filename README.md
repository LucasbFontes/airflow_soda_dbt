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

Attends to Data Build Tool and it's one of the hot topics in the field. 

## Metabase

# The project
