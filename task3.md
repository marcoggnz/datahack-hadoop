# Tarea 3: Estimating Hadoop Architectures for Different Use Cases

<strong>The company is also considering connecting their Big Data platform with other tools within the company, and they are asking for advice on how it could be integrated/executed:
1. Business Intelligence Tool (e.g., Microstrategy)
2. Web for querying orders made
3. Generation of SQL reports using R that are run monthly
4. Collection of social media information

For each of these tasks, indicate which possible tools from the Hadoop ecosystem would be applied according to the requirements of the case, considering the advantages and disadvantages of each (for example, Impala usage consumes a lot of RAM).</strong>

##

## 1. Business Intelligence Tools

Recommended Tools: Hive (with HiveServer2 + JDBC/ODBC) and Impala.

| Tool | Advantages                                                                                                            | Disadvantages                                                        |
|-------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|
| **Hive**    | - Great compatibility (JDBC/ODBC)<br>- Optimized for large analytical queries<br>- Relatively low RAM cost | - High latency for interactive queries (minutes)<br>- Not optimal for frequent interactive dashboards |
| **Impala**  | - Fast interactive queries (seconds)<br>- Good integration with modern BI tools like MicroStrategy<br>- Interactive experience | - High RAM consumption<br>- Needs dedicated resources (high memory)<br>- Cluster management complexity |

Thus, Impala would be used for interactive or real-time dashboards, while Hive would be used if the queries aren’t real-time or if resource optimization is needed.

## 2. Web for querying orders (interactive use)

Recommended Tools: HBase, Apache Phoenix on HBase, and Impala / Hive LLAP.

| Tool | Advantages | Disadvantages |
|-------------------------|---------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **HBase**               |	- Fast key-value queries (milliseconds)<br>- Ideal for frequent low-latency queries<br>- Excellent horizontal scalability | - Does not support SQL queries directly<br>- Difficult for complex analytical queries                                            |
| **Phoenix**             | - Standard SQL support on HBase<br>- Ideal for easy web application integration                                           | - Slightly lower performance than pure HBase                           
                                                              |
| **Impala o Hive LLAP**  | - Fast SQL interactive queries<br>- Ideal for medium complexity analytical queries                                        | - Higher RAM cost (Impala)<br>- Considerable memory and resource cost (Hive LLAP)<br>- Not ideal for frequent transactional queries |

In conclusion, HBase and Phoenix would be used for fast and frequent individual queries; and Impala or Hive LLAP if the queries involve aggregations and medium analytical complexity.

## 3. Monthly SQL report generation using R

Recommended Tools: Hive and SparkSQL.

| Tool           | Advantages                                                                                                                                                                     | Disadvantages                                                                    |
|----------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
| **Hive**       | - Excellent for large batch queries (ideal for monthly reports)<br>- Easy integration from R through JDBC<br>- Relatively low resource consumption compared to in-memory tools | - Slow queries if the complexity is very high (minutes or hours)            |
| **Spark SQL**  | - Very fast when loading data into memory<br>- Perfect for direct integration with R scripts (e.g., SparkR, sparklyr)<br>- Supports a wide variety of statistical functions    | - Higher memory consumption (RAM)<br>- Greater operational complexity |

Hive would allow large SQL reports to be created, ideal for monthly reports with low urgency; while SparkSQL would allow reports requiring complex calculations or advanced R algorithms.

## 4. Recopilación de información de redes sociales (procesamiento en tiempo real o streaming)

Herramientas recomendadas: Apache Kafka, Apache Flume y Apache NiFi.

| Herramienta      | Ventajas                                                                                                                                              | Desventajas                                                                                                |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|
| **Apache Kafka** | - Excellent for real-time data ingestion<br>- Very scalable and robust<br>- Compatible with multiple sources (social media APIs)                      | - Needs additional infrastructure (Kafka clusters)<br>- High operational complexity                        |
| **Flume**        | - Very easy integration with HDFS, ideal for logs/text<br>- Simple setup for basic data                                                               | - Less flexible than Kafka<br>- Not optimal for high ingestion rates or scalability                      |
| **NiFi**         | - Easy visual interface (drag-and-drop)<br>- Excellent for quick integration of complex data sources (social media APIs)<br>- Visual error management | - Slightly lower scalability than Kafka with large volumes<br>- Less efficient at extreme performance |

Thus, Apache Kafka would be used for extreme scalability and robustness; NiFi if visual ease and quick integration with APIs and moderate processing are prioritized; and Flume for simple text/log data collection, seeking absolute simplicity (not recommended for complex APIs).

## Conclusion

In conclusion, Impala is the best tool for BI tasks, allowing fast interactive queries; HBase and Phoenix would be the best tools for querying orders on a web platform, ideal for web app integration; SparkSQL is my choice for generating monthly SQL reports using R, due to its fast execution and ease of integration with R; and finally, Kafka is the best tool for collecting social media data because of its scalability, robustness, and real-time capabilities.

With this information, MovieBuster will be able to assess which tools to choose based on priority: interactive performance, scalability, simplicity, or resource cost.
