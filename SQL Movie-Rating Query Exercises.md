# SQL Movie-Rating Query Exercises (core set)

Movie ( mID, title, year, director )
English: There is a movie with ID number mID, a title, a release year, and a director.

Reviewer ( rID, name )
English: The reviewer with ID number rID has a certain name.

Rating ( rID, mID, stars, ratingDate )
English: The reviewer rID gave the movie mID a number of stars rating (1-5) on a certain ratingDate.

Question 1
Find the titles of all movies directed by Steven Spielberg.
Note: Your queries are executed using SQLite, so you must conform to the SQL constructs supported by SQLite.

```sql
SELECT Movie.title
FROM Movie
WHERE  Movie.Director = "Steven Spielberg";
```
Question 2
Find all years that have a movie that received a rating of 4 or 5, and sort them in increasing order.

```sql
SELECT DISTINCT Movie.year
FROM Rating, Movie
WHERE Rating.stars < 6 AND Rating.stars > 3
AND Movie.mID = Rating.mID
ORDER BY Movie.year;
```

Question 3
Find the titles of all movies that have no ratings.

```sql
SELECT DISTINCT Movie.title
FROM Movie, Rating
WHERE Movie.mID NOT IN(
SELECT Movie.mID
FROM Movie,Rating
WHERE Movie.mID = Rating.mID);
```

Question 4
Some reviewers didn't provide a date with their rating. Find the names of all reviewers who have ratings with a NULL value for the date.

```sql
SELECT Reviewer.name
FROM Reviewer, Rating
WHERE ratingDate IS NULL
AND Reviewer.rID = Rating.rID;
```

Question 5
Write a query to return the ratings data in a more readable format: reviewer name, movie title, stars, and ratingDate. Also, sort the data, first by reviewer name, then by movie title, and lastly by number of stars.

```sql
SELECT DISTINCT Reviewer.name, Movie.title, Rating.stars, Rating.ratingDate
FROM Movie, Reviewer, Rating
WHERE Reviewer.rID = Rating.rID AND Rating.mID = Movie.mID
ORDER BY Reviewer.name, Movie.title, Rating.stars
```
Question 6
For all cases where the same reviewer rated the same movie twice and gave it a higher rating the second time, return the reviewer's name and the title of the movie.

```sql
SELECT Reviewer.name, Movie.title
FROM Rating R1, Rating R2, Reviewer, Movie
WHERE R1.rID= R2.rID
AND R1.RatingDate < R2.RatingDate
AND R1.mID = R2.mID
AND R1.stars < R2.stars
AND R2.mID = movie.mID
AND R2.rID = reviewer.rID ;
```

Question 7
For each movie that has at least one rating, find the highest number of stars that movie received. Return the movie title and number of stars. Sort by movie title.

```sql
SELECT Movie.title, MAX(Rating.stars)
FROM  Movie, Rating
WHERE Movie.mID = Rating.mID
GROUP BY Movie.mID
ORDER BY Movie.title;
```

Question 8
For each movie, return the title and the 'rating spread', that is, the difference between highest and lowest ratings given to that movie. Sort by rating spread from highest to lowest, then by movie title.

```sql
SELECT Movie.title, Max(Rating.stars) - MIN(Rating.stars)
FROM Movie, Rating
WHERE Movie.mID = Rating.mID
GROUP BY Movie.mID
ORDER BY Max(Rating.stars) - MIN(Rating.stars) DESC, Movie.title
```

Question 9
Find the difference between the average rating of movies released before 1980 and the average rating of movies released after 1980. (Make sure to calculate the average rating for each movie, then the average of those averages for movies before 1980 and movies after. Don't just calculate the overall average rating before and after 1980.)

```sql
select max(a1)-min(a1) from
(select avg(av1) a1 from
(select avg(stars) av1 from rating r join movie m on r.mid=m.mid where m.year < 1980
group by r.mid)
union
select avg(av2) a1 from
(select avg(stars) av2 from rating r join movie m on r.mid=m.mid where m.year > 1980
group by r.mid))
```
