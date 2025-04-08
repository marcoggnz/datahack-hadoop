# Task 1: Cluster Deployment and Query Execution

As mentioned in the [README.md](README.md), this task was completed on a virtual machine configured in Cloudera.

## Data Ingestion in Hadoop

First, open the command line and create a new directory named <code>dataset-hadoop</code> in the path <code>/home/cloudera/dh-course/dataset-hadoop</code>. Then, copy the three files that will be used throughout the task from the repository https://github.com/dgarciaesc/sample_dataset:

* <strong>movies.dat</strong>: contains information about movies, including their ID, title, and genre.
* <strong>users.dat</strong>: contains information about users who rate the movies. This database includes the ID of each user, gender, age, profession, and postal code.
* <strong>ratings.dat</strong>: contains information about the user ID, movie ID, rating, and timestamp.
> **Note:** Detailed information about the structure and variables of these 3 files can be found in [README.txt](https://github.com/marcoggnz/hadoop/blob/main/data/README.txt).

![image](https://github.com/user-attachments/assets/e89e96fd-6210-4970-aa5a-47dc18cd5ba4)

You can check that the data is stored correctly by displaying the content of the first rows of each file:

```
head -n 5 ratings.dat
head -n 5 users.dat
head -n 5 movies.dat
```

![image](https://github.com/user-attachments/assets/cb7fad58-26fd-4566-86e9-b7639b525b43)

Once the files are in the created directory, make sure there are no permission issues during the task development. Ensure read, write, and execute permissions for all these files by running the following commands:

```
chmod 777 movies.dat
chmod 777 ratings.dat
chmod 777 users.dat
```
## Database Creation

Once the environment is set up, the first step is to create the database in Hive. To do this, access the Hive environment:

```
hive
```

Once inside Hive, create the database:

```
CREATE DATABASE IF NOT EXIST ex1;
SHOW DATABASES;
USE ex1;
```

This database is stored at the path <code>/usr/hive/warehouse</code>. Next, create the tables with the data to be used.

### Movies Table

To create the movies table, use the following commands:

```
CREATE TABLE movies (
    movie_id INT,
    title STRING,
    genres STRING
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.contrib.serde2.MultiDelimitSerDe'
WITH SERDEPROPERTIES ("field.delim"="::");
```

The three data files use double colons (::) as column delimiters, so keep this in mind when creating the tables.

Next, move the <code> movies.dat</code> file from the local filesystem to HDFS using the command:

```
hdfs dfs -put /home/cloudera/dh-course/dataset-hadoop/movies.dat /user/cloudera/movies/
```

### Users Table

Similarly to how the movies table was created, create the users table:

```
CREATE TABLE users (
    user_id INT,
    gender STRING,
    age INT,
    occupation INT,
    zip_code STRING
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.contrib.serde2.MultiDelimitSerDe'
WITH SERDEPROPERTIES ("field.delim"="::");
```

This table is located at the path <code>/user/hive/warehouse/ex1.db</code>. Next, copy the users data file from the local directory to the HDFS environment:

```
hdfs dfs -put /home/cloudera/dh-course/dataset-hadoop/users.dat /user/cloudera/users/
```

### Ratings Table

Lastly, create the third table (ratings) with the following command:

```
CREATE TABLE ratings (
    user_id INT,
    movie_id INT,
    rating INT,
    timestamp INT
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.contrib.serde2.MultiDelimitSerDe'
WITH SERDEPROPERTIES ("field.delim"="::");
```

This table is stored in the same path as the other tables: <code>/user/hive/warehouse/ex1.db</code>. Once this table is created, load the data from the ratings file into Hive:

```
hdfs dfs -put /home/cloudera/dh-course/dataset-hadoop/ratings.dat /user/cloudera/ratings/
```

To finish preparation, load the data into the Hive tables with the following commands:

```
LOAD DATA INPATH '/user/cloudera/movies/movies.dat' INTO TABLE movies;
LOAD DATA INPATH '/user/cloudera/users/users.dat' INTO TABLE users;
LOAD DATA INPATH '/user/cloudera/ratings/ratings.dat' INTO TABLE ratings;
```

A simple query can check that the data has been stored correctly:

```
SELECT * FROM movies LIMIT 10;
SELECT * FROM users LIMIT 10;
SELECT * FROM ratings LIMIT 10;
```

![image](https://github.com/user-attachments/assets/2992fa5d-4763-42f8-bcf0-3824f0cc5e2e)

### Exercise 1

<strong>To define which genre to bet on, identify influencers who can boost MovieBuster's marketing, and define an advertising strategy, the CEO asks you to find out the following current market data:</strong>

<strong>1. What is the movie with the most reviews?</strong>

Query used:

```
SELECT m.title, COUNT(*) AS num_ratings
FROM ratings r
JOIN movies m ON r.movie_id = m.movie_id
GROUP BY m.title
ORDER BY num_ratings DESC
LIMIT 1;
```

![image](https://github.com/user-attachments/assets/aa41c802-b03c-45ae-9fe5-d652de82be58)

The movie with the most reviews is "American Beauty" with a total of 3428 reviews.

<strong>2. What are the top 10 users most active in rating movies?

Query used:

```
SELECT user_id, COUNT(*) AS total_reviews
FROM ratings
GROUP BY user_id
ORDER BY total_reviews DESC
LIMIT 10;
```

![image](https://github.com/user-attachments/assets/e8d142b1-e2bd-44ed-97bb-75b2b75e5f1b)

The top 10 user IDs who have posted the most reviews are 4169, 1680, 4277, 1941, 1181, 889, 3618, 2063, 1150, and 1015.

<strong>3. What are the top three movies according to their ratings? And the bottom three?</strong>

Query used for the top 3 movies:
> **Note:** For this query, we considered movies with at least 100 reviews.
```
SELECT m.title, AVG(r.rating) AS avg_rating, COUNT(*) AS total_ratings
FROM ratings r
JOIN movies m ON r.movie_id = m.movie_id
GROUP BY m.title
HAVING COUNT(*) >= 100
ORDER BY avg_rating DESC
LIMIT 3;
```

![image](https://github.com/user-attachments/assets/b58da9c8-c8a9-4ed0-8581-3db503e99f67)

The top 3 movies (with at least 100 reviews) according to ratings are "Seven Samurai" (The Magnificent Seven), "The Shawshank Redemption", and "The Godfather", with ratings of 4.56, 4.55, and 4.52, respectively.

Query used for the bottom 3 movies:
> **Note:** For this query, we considered movies with at least 100 reviews.

```
SELECT m.title, AVG(r.rating) AS avg_rating, COUNT(*) AS total_ratings
FROM ratings r
JOIN movies m ON r.movie_id = m.movie_id
GROUP BY m.title
HAVING COUNT(*) >= 100
ORDER BY avg_rating ASC
LIMIT 3;
```

![image](https://github.com/user-attachments/assets/26224197-977b-402a-a8d6-80d2a7fcbe60)

The bottom 3 movies (with at least 100 reviews) according to ratings are "Kazaam", "Battlefield Earth", and "Pokemon the Movie", with ratings of 1.47, 1.61, and 1.62, respectively.

<strong>4. Is there any profession where we should focus our advertising efforts? Why?</strong>

Query used:

```
SELECT u.occupation, COUNT(*) AS total_ratings
FROM ratings r
JOIN users u ON r.user_id = u.user_id
GROUP BY u.occupation
ORDER BY total_ratings DESC
LIMIT 1;
```

![image](https://github.com/user-attachments/assets/6d5d3f28-1a9d-44d5-b480-b687d71b9750)

The profession with the most ratings is number 4. According to the file [README.txt](data/README.txt), this is the profession of "college/grad student".

<strong>5. Do you have any other valuable insight we could extract from the processed data? How?</strong>

One interesting query would be the relationship between the user's age and their ratings. The query used is:

```
SELECT u.age, AVG(r.rating) AS avg_rating
FROM ratings r
JOIN users u ON r.user_id = u.user_id
GROUP BY u.age
ORDER BY u.age;
```

![image](https://github.com/user-attachments/assets/3f22e729-2cd0-4527-9304-1bd0bf81419d)

From the results, the following conclusions can be drawn:

| Age   | Avg Rating   | Interpretation                                            |
|-------|--------------|-----------------------------------------------------------|
| <18   | 3.55         | Young people rate well, but not excessively               |
| 18-24 | 3.51         | Young adults are the most critical (lowest rating)        |
| 24-34 | 3.55         | Very similar to those under 18                            |
| 35-44 | 3.62         | Rating increases. Perhaps less critical or broader tastes |
| 44-49 | 3.64         | Even higher                                               |
| 50-55 | 3.71         | Much more positive                                        |
| >56   | 3.77         | The most positive of all                                  |

### Exercise 2

<strong>The CEO is concerned about the efficiency of the queries used to extract data for exercises #1 and #2 and wants to be able to view these results on a website.</strong>

<strong>1. Implement, through Sqoop, a relational database in MySQL that contains at least the data from one of the insights extracted in exercise #1</strong>

For this exercise, we chose the insight "Top 10 most active users in rating movies" from Exercise 1.

First, re-run the query and save the result in the <code>top_users</code> table:

```
CREATE TABLE top_users AS
SELECT user_id, COUNT(*) AS num_ratings
FROM ratings
GROUP BY user_id
ORDER BY num_ratings DESC
LIMIT 10;
```

Confirm that the table has been created correctly:

```
SELECT * FROM top_users;

```

![image](https://github.com/user-attachments/assets/cc99ed0d-bfbf-469c-bdbd-e5fee474edc6)

Next, access MySQL with the following command:

```
mysql -uroot -pcloudera
```

Then, create a new database named  <code>ex1</code> in MySQL:

```
CREATE DATABASE ex1;
```

![image](https://github.com/user-attachments/assets/41cb158e-a369-45d8-98b9-85ce4d6ec813)

Change the database to the one just created:

```
USE ex1;
```
 
Within this database, create the <code>top_users</code> table:

```
CREATE TABLE top_users (
  user_id INT,
  num_ratings INT
);
```

Lastly, use Sqoop to export the table from Hive to MySQL by running the following command on the local file system:
 
```
sqoop export --connect jdbc:mysql://localhost/ex1 --table top_users --export-dir /user/hive/warehouse/ex1.db/top_users_export --input-fields-terminated-by '\t' --username root --password cloudera --num-mappers 1 
```

Once the export is done, check the results in MySQL:

![image](https://github.com/user-attachments/assets/264f054a-46ab-42d9-9940-e2df47164b61)

The table should now be correctly created in MySQL. The next step would be to program a small app in Python or Node.js to connect to MySQL and display the results, or use a visualization tool like Grafana.

<strong>2. Why do we do this and not access the results directly from exercise #1?</strong>

There are several reasons why exporting the data to MySQL is a better option if we want to visualize it on a web application. The table below shows the main characteristics of HIVE/HDFS and MySQL (RDBMS):

| Hive/HDFS                                         | MySQL/RDBMS                                       |
|---------------------------------------------------|---------------------------------------------------|
| Designed for batch processing in Big Data         | Designed for fast access to structured data       |
| Very high latency for small queries               | 	Very low latency for fast queries               |
| Not suitable for serving real-time data (web/API) | Ideal for serving results to a web or application |
| Great storage capacity                            | Excellent integration with frontends and APIs     |
| Does not support frequent updates/inserts         | Ideal for dynamic changes and transactions        |

Therefore, while Hive is ideal for large-scale data processing and analysis, MySQL performs much more efficiently when fast queries and real-time web access are needed.
