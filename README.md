# Microblogging Use Case
My personal data engineer project of make a microblogging like twitter.

## Database Design for Microblogging Use-Case:
1. Imagine your team will make a microblogging like twitter. People can tweet, reply, like,
and retweet. Please,
  a. Design a database with the relationship for microblogging use-case.
  b. From the database provides the query for,
    - Creating all tables
    - Showing all tweets from specific user includes reply, like, retweet counts
    - Showing specific tweet information, includes all replies, who like the tweet, and lo retweet the tweet.
c. [optional] Create diagram if the company need to create data warehouse from
existing database. You can use any stack you confident. 

## Let's start!
1. Based on the given use-case, we can design the following database schema:
   
   a. User Table:
   
    - user_id (Primary Key)
    - username
    - email
    - password
   
   b. Tweet Table:
  
    - tweet_id (Primary Key)
    - user_id (Foreign Key referencing User table)
    - content
    - created_at
   
   c. Reply Table:
     
    - reply_id (Primary Key)
    - tweet_id (Foreign Key referencing Tweet table)
    - user_id (Foreign Key referencing User table)
    - content
    - created_at
   
   d. Like Table:
     
    - like_id (Primary Key)
    - tweet_id (Foreign Key referencing Tweet table)
    - user_id (Foreign Key referencing User table)
    - created_at
   
   e. Retweet Table:
     
    - retweet_id (Primary Key)
    - tweet_id (Foreign Key referencing Tweet table)
    - user_id (Foreign Key referencing User table)
    - created_at

2. Query Examples:

   a. Creating all tables:
   
      ```
      CREATE TABLE User (
      	user_id INT PRIMARY KEY,
      	username VARCHAR(255),
      	email VARCHAR(255),
      	password VARCHAR(255)
      );
      
      CREATE TABLE Tweet (
      	tweet_id INT PRIMARY KEY,
      	user_id INT,
      	content VARCHAR(255),
      	created_at TIMESTAMP,
      	FOREIGN KEY (user_id) REFERENCES User(user_id)
      );
      
      CREATE TABLE Reply (
      	reply_id INT PRIMARY KEY,
      	tweet_id INT,
      	user_id INT,
      	content VARCHAR(255),
      	created_at TIMESTAMP,
      	FOREIGN KEY (tweet_id) REFERENCES Tweet(tweet_id),
      	FOREIGN KEY (user_id) REFERENCES User(user_id)
      );
      
      CREATE TABLE Like (
      	like_id INT PRIMARY KEY,
      	tweet_id INT,
      	user_id INT,
      	created_at TIMESTAMP,
      	FOREIGN KEY (tweet_id) REFERENCES Tweet(tweet_id),
      	FOREIGN KEY (user_id) REFERENCES User(user_id)
      );
      
      CREATE TABLE Retweet (
      	retweet_id INT PRIMARY KEY,
      	tweet_id INT,
      	user_id INT,
      	created_at TIMESTAMP,
      	FOREIGN KEY (tweet_id) REFERENCES Tweet(tweet_id),
      	FOREIGN KEY (user_id) REFERENCES User(user_id)
      );
      ```

   b. Showing all tweets from a specific user includes reply, like, and retweet counts:
   
      ```
      SELECT t.tweet_id, t.content, COUNT(r.reply_id) AS reply_count, COUNT(l.like_id) AS like_count, COUNT(rt.retweet_id) AS retweet_count
      FROM Tweet t
      LEFT JOIN Reply r ON t.tweet_id = r.tweet_id
      LEFT JOIN Like l ON t.tweet_id = l.tweet_id
      LEFT JOIN Retweet rt ON t.tweet_id = rt.tweet_id
      WHERE t.user_id = <user_id>
      GROUP BY t.tweet_id, t.content;
      ```

   c. Showing specific tweet information includes all replies, who liked the tweet, and who retweeted the tweet:

     ```
     SELECT t.tweet_id, t.content, u.username AS user_liked, u2.username AS user_retweeted, r.content AS reply_content
     FROM Tweet t
     LEFT JOIN Like l ON t.tweet_id = l.tweet_id
     LEFT JOIN User u ON l.user_id = u.user_id
     LEFT JOIN Retweet rt ON t.tweet_id = rt.tweet_id
     LEFT JOIN User u2 ON rt.user_id = u2.user_id
     LEFT JOIN Reply r ON t.tweet_id = r.tweet_id
     WHERE t.tweet_id = <tweet_id>;
     ```

3. Here is an example of a data warehouse diagram, for raw files, can be accessed [here](https://github.com/bzizmza/microblogging-use-case/blob/main/Warehouse.png).

    ![](https://github.com/bzizmza/microblogging-use-case/blob/main/Warehouse.png)

4. In the data warehouse diagram above, we have the following components:

    - User Dim: This dimension table contains user-related information, such as user_id, username, and email.
    - Tweet Dim: This dimension table contains tweet-related information, such as tweet_id, user_id (foreign key referencing User Dim), content, and created_at.
    - Date Dim: This dimension table contains date-related information, such as date_id and date. This table can be used to analyze trends and time-based metrics.
    - Reply Fact: This fact table captures the replies to tweets. It contains reply_id, tweet_id (foreign key referencing Tweet Dim), user_id (foreign key referencing User Dim), content, and created_at.
    - Like Fact: This fact table captures the likes on tweets. It contains like_id, tweet_id (foreign key referencing Tweet Dim), user_id (foreign key referencing User Dim), and created_at.
    - Retweet Fact: This fact table captures the retweets of tweets. It contains retweet_id, tweet_id (foreign key referencing Tweet Dim), user_id (foreign key referencing User Dim), and created_at.

5. This data warehouse diagram represents the dimensional model for the microblogging use-case, allowing for efficient analysis and reporting on various metrics related to users, tweets, replies, likes, and retweets.
