# MovieLens Analysis with Spark and Cassandra

This project uses the MovieLens 100K dataset.
I analysed the data with PySpark and saved all results to Cassandra.
I also did a Extension work with MongoDB
I also did a visualization for the top 10 movies to show their rating_count.

For environment setup and troubleshooting, please read [SETUP.md](SETUP.md).

## What I did

I first uploaded the three MovieLens files to HDFS.  

<img width="735" height="262" alt="image" src="https://github.com/user-attachments/assets/f088d93e-d4fd-4e43-a4c6-52369696cf2a" />

After that I read the data into Spark DataFrames, cleaned it, and ran five tasks.

## Tasks and findings

- **Task i – Average rating per movie**
  I calculated the average rating for each movie. Movies with very few ratings can show extreme values.
- **Task ii – Top 10 movies by average rating**
  The top 10 all had a perfect 5, but each had only one to three votes. Without a minimum vote count the ranking is not reliable.
- **Task iii – Favorite genre of active users (≥50 ratings)** 
  Drama was the most common favourite genre, because the catalogue contains more Drama movies than any other genre.
- **Task iv – Users under 20**
  Almost all of them are students.
- **Task v – Scientists aged 30–40**
  The group is very small, and the gender split is heavily unbalanced (only one female).

All five results were written to Cassandra.  
Here is a screenshot of the keyspace and tables in cqlsh.

<img width="1337" height="329" alt="image" src="https://github.com/user-attachments/assets/5da3ced9-e5ad-4874-98df-6346ee8dfbcd" />

Here, i load 10 records from Cassandra from the results of task1.

<img width="1337" height="323" alt="image" src="https://github.com/user-attachments/assets/c66425d7-eb4d-41b3-b476-fcfd732f0d03" />


## MongoDB practice

I also wrote the user data into MongoDB and ran a few queries to find users in different age ranges.  
I changed the age filter to 20‑30 and saw that the student ratio was still high, but other jobs like artist and writer started to appear.

Here, I've read some data from MongoDB.
<img width="1306" height="721" alt="image" src="https://github.com/user-attachments/assets/6c8f2bb6-2678-4f3a-87f8-7237235d09d4" />

## Visual result

This pictures hows the number of ratings for the top 10 movies

<img width="712" height="424" alt="image" src="https://github.com/user-attachments/assets/11869ebb-8538-46c8-9506-a880eb949995" />

## Environment

- Python 2.7
- Apache Spark 2.3.0
- Apache Cassandra 3.11.16
- MongoDB


