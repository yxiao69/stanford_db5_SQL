# SQL Movie-Rating Query Exercises (extra)

Question 1
Find the names of all reviewers who rated Gone with the Wind.

```sql
SELECT DISTINCT Reviewer.name
FROM Reviewer, Rating, Movie
WHERE Reviewer.rID = Rating.rID
AND Movie.mID = Rating.mID
AND Movie.title = "Gone with the Wind"
```

Question 2
For any rating where the reviewer is the same as the director of the movie,
return the reviewer name, movie title, and number of stars.

```sql
SELECT Reviewer.name, Movie.title, Rating.stars
FROM Reviewer, Movie, Rating
WHERE Reviewer.name = Movie.director
AND Reviewer.rID = Rating.rID
AND Movie.mID = Rating.mID;
```

Question 3
Return all reviewer names and movie names together in a single list, alphabetized.
(Sorting by the first name of the reviewer and first word in the title is fine;
no need for special processing on last names or removing "The".)

```sql
SELECT Reviewer.name
FROM Reviewer
UNION
SELECT Movie.title
FROM Movie
```

Question 4
Find the titles of all movies not reviewed by Chris Jackson.

```sql
SELECT DISTINCT Movie.title
FROM Movie
EXCEPT
SELECT  Movie.title
FROM Movie, Reviewer, Rating
WHERE Reviewer.name = "Chris Jackson"
AND Reviewer.rID = Rating.rID
AND Movie.mID = Rating.mID
```

Question 5
For all pairs of reviewers such that both reviewers gave a rating to the same movie, return the names of both reviewers. Eliminate duplicates, don't pair reviewers with themselves, and include each pair only once. For each pair, return the names in the pair in alphabetical order.

```sql
SELECT DISTINCT R1.name, R2.name
FROM Reviewer R1, Reviewer R2, Rating RT1, Rating RT2
WHERE R1.name < R2.name
AND RT1.rID = R1.rID
AND RT2.rID = R2.rID
AND RT1.mID = RT2.mID
ORDER BY R1.name
```

Question 6
For each rating that is the lowest (fewest stars) currently in the database, return the reviewer name, movie title, and number of stars.

```sql
SELECT Reviewer.name, Movie.title, Rating.stars
FROM Reviewer, Movie, Rating
WHERE Rating.stars = (
SELECT MIN(Rating.stars)
FROM Rating
)
AND Rating.rID = Reviewer.rID
AND Rating.mID = Movie.mID
```

Question 7
List movie titles and average ratings, from highest-rated to lowest-rated.
If two or more movies have the same average rating, list them in alphabetical order.

```sql
SELECT Movie.title, avg(Rating.stars)
FROM Movie, Rating
WHERE Rating.mID = Movie.mID
GROUP BY Rating.mID
ORDER BY avg(Rating.stars) desc, Movie.title
```

Question 8
Find the names of all reviewers who have contributed three or more ratings.
(As an extra challenge, try writing the query without HAVING or without COUNT.)

```sql
SELECT Reviewer.name
FROM Reviewer, Rating
WHERE Rating.rID = Reviewer.rID
GROUP BY Rating.rID
Having COUNT(Rating.rID) >= 3
```

Question 9
Some directors directed more than one movie. For all such directors, return the titles of all movies directed by them, along with the director name.
Sort by director name, then movie title. (As an extra challenge, try writing the query both with and without COUNT.)

```sql
SELECT Movie.title, Movie.director
FROM
(SELECT Movie.director
FROM Movie
GROUP BY Movie.director
Having COUNT(Movie.director)>1
) AS M1
INNER JOIN Movie
WHERE Movie.director = M1.director
ORDER BY Movie.director, Movie.title
```

Question 10
Find the movie(s) with the highest average rating. Return the movie title(s) and average rating.
(Hint: This query is more difficult to write in SQLite than other systems;
you might think of it as finding the highest average rating and then choosing the movie(s) with that average rating.)

```sql
SELECT Movie.title, Movie.director
FROM
(SELECT Movie.director
FROM Movie
GROUP BY Movie.director
Having COUNT(Movie.director)>1
) AS M1
INNER JOIN Movie
WHERE Movie.director = M1.director
ORDER BY Movie.director, Movie.title
```

Question 11
Find the movie(s) with the lowest average rating. Return the movie title(s) and average rating.
(Hint: This query may be more difficult to write in SQLite than other systems; you might think of it as finding the highest average rating and then choosing the movie(s) with that average rating.)

```sql
SELECT M.title, AVG(R.stars)
FROM Movie M, Rating R
WHERE M.mID = R.mID
GROUP BY R.mID
HAVING AVG(R.stars) = (
SELECT MIN(S.stars)
FROM (
SELECT AVG(Rating.stars) AS stars
FROM Rating
GROUP BY Rating.mID
) AS S
)
```

Question 12
For each director, return the director's name together with the title(s) of the movie(s) they directed that received
the highest rating among all of their movies, and the value of that rating. Ignore movies whose director is NULL.

```sql
select director, title, stars
from movie m, rating r
where m.mid = r.mid and director is not null
group by director
order by stars desc
```
