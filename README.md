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