# SQL CRUD
#### Anas Moazzam

## Part 1: Restaurant Finder

### Practice Data
- RestaurantID = Unique ID for each restaurant based on an integer from 1 to 1000
- RestaurantName =  Name of Restaurant
- Category = Types of cuisine (eg: Japanese, Mexican, etc.)
- Neighborhood = Various neighborhoods within NYC (eg: Astoria, Upper East Side, etc.)
- Price Tier = Three levels of price: cheap, medium, expensive
- Good For Kids = A boolean value of True or False depending on if the restaurant is good for kids
- AverageRating = The average star rating for each restaurant ranging from 1 through 5
- Opening Hours = Opening times in 24HR ranging format from 9AM to 11:59AM
- Closing Hours = Closing times in 24HR ranging format from 7:30PM to 11:59PM

### Creating Tables
I start off by creating my database for restaurants.
```sql
sqlite3 restaurants.db
```
Then, I create my table for restaurants.
```sql
CREATE TABLE restaurants (
    RestaurantID INT PRIMARY KEY,
    RestaurantName TEXT,
    Category TEXT,
    Neighborhood TEXT,
    PriceTier TEXT,
    GoodForKids BOOLEAN,
    AverageRating REAL,
    OpeningHour TIME,
    ClosingHour Time
);
```
Next, I create my table for reviews.

```SQL
CREATE TABLE reviews (
    ReviewID INT PRIMARY KEY,
    RestaurantID INT,
    Review TEXT,
    Rating REAL,
    FOREIGN KEY (RestaurantID) REFERENCES restaurants(RestaurantID)
);
```
Now, I import my restaurants data into my database.
```SQL
.mode csv
.import ./data/restaurants.csv restaurants
```

### Queries
- Find all cheap restaurants in a particular neighborhood (In this case: Astoria).

```sql
SELECT *
FROM restaurants
WHERE PriceTier = 'Cheap'
AND Neighborhood = 'Astoria';
```

- Find all restaurants in a particular genre (Thai) with 3 stars or more, ordered by the number of stars in descending order.

```sql
SELECT *
FROM restaurants
WHERE Category = 'Thai'
AND AverageRating >= 3
ORDER BY AverageRating DESC;
```

- Find all restaurants that are open now
```sql
SELECT *
FROM Restaurants
WHERE strftime('%H:%M', 'now', 'localtime') BETWEEN OpeningHour AND ClosingHour;
```

- Leave a review for a restaurant. In this case, I want to review Cafe Caliente.
```sql
INSERT INTO reviews (RestaurantID, Review, Rating) 
VALUES (2, 'The food was super spicy but delicious', 4.5);
```

- Delete all restaurants that are not good for kids.
```sql
DELETE FROM restaurants
WHERE GoodForKids = False;
```

- Find the number of restaurants in each NYC neighborhood.
```sql
SELECT Neighborhood, COUNT(RestaurantID) AS rest_sum 
FROM restaurants 
GROUP BY Neighborhood 
ORDER BY rest_sum;
```

This is the table that I got:

| Neighborhood      | Sum of Restaurants    |
|-------------------|-----------------------|
| Astoria           | 86                    |
| Bed-Stuy          | 92                    |
| Bushwick          | 91                    |
| Chelsea           | 128                   |
| Greenwich Village | 100                   |
| Harlem            | 99                    |
| Long Island City  | 114                   |
| Park Slope        | 96                    |
| Upper East Side   | 105                   |
| Williamsburg      | 89                    |


## Part 2: Social Media App

### Practice Data
Users Dataset
- id = Unique ID for each user from 1 through 1000
- email =  Unique email for each user
- username = unique username for each user
- password = password for each user

Posts Dataset
- id = Unique ID for each activity on platform
- sender_id = the user ID for the user sending the message or story
- reciever_id = the user ID for the user recieving the message or story
- content = content of post
- content_type = labeling whether it was a message or story
- privacy = True if private, False if not
- time_posted = The date and time when the content was posted / sent

### Creating Tables
I start off by creating my database for the social media app.
```sql
sqlite3 socialmedia.db
```
Then, I create my table for users.
```sql
CREATE TABLE users (
    id INT PRIMARY KEY AUTOINCREMENT,
    email TEXT UNIQUE,
    username TEXT UNIQUE,
    password TEXT
);
```

Then, I create a table for posts.
```sql
CREATE TABLE posts (
    id INT PRIMARY KEY AUTOINCREMENT,
    sender_id INT,
    reciever_id INT,
    content TEXT,
    content_type TEXT,
    privacy BOOLEAN,
    time_posted DATETIME,
    FOREIGN KEY (sender_id) REFERENCES users(id),
    FOREIGN KEY (recipient_id) REFERENCES users(id),
); 
```

Now, I import the necessary datafiles into my databases.
```sql
.mode csv
import ./data/users.csv users
import ./data/posts.csv posts
```

### Queries

- Registering a New User
```sql
INSERT INTO users (email, username, password)
VALUES ('new.user@nyu.edu', 'newuser', 'Password!');
```

- Create a new Message sent by User 200 to User 847.
```sql
INSERT INTO posts (sender_id, reciever_id, content, content_type, privacy, time_posted)
VALUES (200, 847, 'heyy', 'message', 'true', datetime('now'));
```

- Create a new Story by user 200.
```sql
INSERT INTO posts (sender_id, content, content_type, privacy, time_posted)
VALUES (200, 'who want me ;)', 'story', 'false', datetime('now'));
```

- Show the 10 most recent visible Messages and Stories, in order of recency.
```sql
SELECT * 
FROM posts 
WHERE privacy = False 
ORDER BY time_posted
DESC LIMIT 10;
```

- Show the 10 most recent visible Messages sent by User 200 to a User 847.
```sql
SELECT *
FROM posts 
WHERE sender_id = 200  
AND reciever_id = 847 
AND content_type = 'Message' 
AND privacy = False 
ORDER BY time_posted DESC LIMIT 10;
```

- Make all Stories that are more than 24 hours old invisible.
```sql
UPDATE posts 
SET privacy = True 
WHERE content_type = 'story' 
AND (JULIANDAY('now') - JULIANDAY(created_at)) * 24 > 24;
```

- Show all invisible Messages and Stories, in order of recency.
```sql
SELECT *
FROM posts 
WHERE privacy = True  
ORDER BY time_posted DESC;
```

- Show the number of posts by each User.
```sql
SELECT user_id, COUNT(content_type) AS post_sum 
FROM posts 
GROUP BY user_id
```

- Show the post text and email address of all posts and the User who made them within the last 24 hours.
```sql
SELECT posts.content, users.email
FROM posts
JOIN users
on posts.sender_id = users.id
WHERE (JULIANDAY('now') - JULIANDAY(created_at)) * 24 > 24
ORDER BY posts.time_posted DESC;
```

- Show the email addresses of all Users who have not posted anything yet.
```sql
SELECT users.email
LEFT JOIN posts ON users.id = posts.sender_id
WHERE users.id NOT IN (SELECT sender_id FROM posts);
```