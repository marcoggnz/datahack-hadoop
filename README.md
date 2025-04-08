# Analysis of the Current Movie Market Using Hadoop

This project applies the basic concepts and notions of Hadoop through three tasks, two theoretical and one practical. The goal is to analyze the current movie market for a startup and derive insights using a public dataset from [MovieLens](https://grouplens.org/datasets/movielens/). This project is part of the Master's in Big Data Architecture & Engineering at Datahack.

## Technologies used
* Hadoop
* Hive
* Sqoop
* MySQL

## Introduction

Currently, data extraction and analysis are fundamental for decision-making in businesses. However, the amount of data generated every day is huge, making the processing and analysis of this data a difficult and costly task. Hadoop is a technology that has revolutionized the way companies extract information and analyze data.

Hadoop is an open-source distributed processing framework that allows for the storage and processing of large amounts of data in computer clusters. With Hadoop, it is possible to process large datasets in parallel, which reduces processing time and cost. Additionally, Hadoop uses a distributed file system that allows for efficient data storage.

One of the most popular use cases of Hadoop is processing user data to implement recommendation systems in companies like Netflix and Spotify. With Hadoop, it is possible to efficiently process user preferences, allowing these companies to provide accurate and personalized recommendations to their users...

## Business Approach

The main objective of this project is to create the analytics department from scratch. This department's primary function is to extract and analyze the necessary data to generate insights that will allow the executive board to define the startup's strategy.
The project consists of three tasks. The development of each of them is detailed below in the following files:
* [Task 1: Cluster Deployment and Query Execution](task1.md)
* [Task 2: Sizing the Hadoop Cluster](task2.md)
* [Tarea 3: Estimating Hadoop Architectures for Different Use Cases](task3.md)

> **Note:** This project was developed on a local Cloudera virtual machine, which is a preconfigured cluster.

## References

* https://www.databricks.com/glossary/hadoop-distributed-file-system-hdfs
* https://www.projectpro.io/article/impala-vs-hive-difference-between-sql-on-hadoop-components/180
* https://phoenix.apache.org/
* *Understanding ETL: Data Pipelines for Modern Data Architectures* by O’Reilly
