


 


You've started a new movie-rating website, and you've been collecting data on reviewers' ratings of various movies. There's not much data yet, but you can still try out some interesting queries. Here's the schema:

Movie ( mID, title, year, director )

English: There is a movie with ID number mID, a title, a release year, and a director.

Reviewer ( rID, name )

English: The reviewer with ID number rID has a certain name.

Rating ( rID, mID, stars, ratingDate )

English: The reviewer rID gave the movie mID a number of stars rating (1-5) on a certain ratingDate.

Your queries will run over a small data set conforming to the schema. View the database. (You can also download the schema and data.)

```
Movie
mID	title	year	director
101	Gone with the Wind	1939	Victor Fleming
102	Star Wars	1977	George Lucas
103	The Sound of Music	1965	Robert Wise
104	E.T.	1982	Steven Spielberg
105	Titanic	1997	James Cameron
106	Snow White	1937	<null>
107	Avatar	2009	James Cameron
108	Raiders of the Lost Ark	1981	Steven Spielberg

Reviewer
rID	name
201	Sarah Martinez
202	Daniel Lewis
203	Brittany Harris
204	Mike Anderson
205	Chris Jackson
206	Elizabeth Thomas
207	James Cameron
208	Ashley White

Rating
rID	mID	stars	ratingDate
201	101	2	2011-01-22
201	101	4	2011-01-27
202	106	4	<null>
203	103	2	2011-01-20
203	108	4	2011-01-12
203	108	2	2011-01-30
204	101	3	2011-01-09
205	103	3	2011-01-27
205	104	2	2011-01-22
205	108	4	<null>
206	107	3	2011-01-15
206	106	5	2011-01-19
207	107	5	2011-01-20
208	104	3	2011-01-02
```

# Q1

Find the titles of all movies directed by Steven Spielberg.

```sql
SELECT title FROM Movie
WHERE director = "Steven Spielberg"
```



# Q2


```sql
SELECT DISTINCT Movie.year
FROM Rating
JOIN Movie
ON Rating.mID = Movie.mID and Rating.stars IN (4,5)
ORDER BY Movie.year ASC
```


# Q3

Find the titles of all movies that have no ratings.


```sql
SELECT title
FROM Movie
WHERE mID NOT IN (SELECT mID FROM Rating)
```


# Q4

Some reviewers didn't provide a date with their rating. Find the names of all reviewers who have ratings with a NULL value for the date.


```sql
SELECT Reviewer.name
FROM Rating
JOIN Reviewer
ON Rating.rID = Reviewer.rID AND ratingDate IS NULL
```



# Q5

Write a query to return the ratings data in a more readable format: reviewer name, movie title, stars, and ratingDate. Also, sort the data, first by reviewer name, then by movie title, and lastly by number of stars.


```sql
SELECT Reviewer.name, Movie.title, Rating.stars, Rating.ratingDate
FROM Rating 
JOIN Movie
ON Rating.mID =  Movie.mID
JOIN Reviewer
ON Rating.rID = Reviewer.rID
ORDER BY Reviewer.name, Movie.title, Rating.stars
```


# Q6

For all cases where the same reviewer rated the same movie twice and gave it a higher rating the second time, return the reviewer's name and the title of the movie.

```
SELECT Reviewer.name, Movie.title
FROM Movie
JOIN Reviewer
JOIN Rating as R1
JOIN Rating as R2
ON Movie.mID = R1.mID AND Movie.mID = R2.mID AND Reviewer.rID = R1.rID AND Reviewer.rID = R2.rID AND R1.rID = R2.rID AND R1.ratingDate < R2.ratingDate AND R1.stars < R2.stars  
```



# Q7

For each movie that has at least one rating, find the highest number of stars that movie received. Return the movie title and number of stars. Sort by movie title.


```sql
SELECT t.title, t.star
FROM
(SELECT Movie.title, COUNT(*) as cnt, MAX(Rating.stars) as star
FROM Movie
JOIN Rating 
ON Movie.mID = Rating.mID
GROUP BY Movie.mID
HAVING COUNT(*) >= 1) AS t
ORDER BY t.title
```



# Q8

List movie titles and average ratings, from highest-rated to lowest-rated. If two or more movies have the same average rating, list them in alphabetical order.



```sql
SELECT Movie.title, AVG(Rating.stars) as avg
FROM Movie
JOIN Rating
ON Movie.mID = Rating.mID
GROUP BY Movie.mID
ORDER BY avg DESC, Movie.title ASC
```



# Q9. 

Find the names of all reviewers who have contributed three or more ratings. (As an extra challenge, try writing the query without HAVING or without COUNT.)


```sql
SELECT tmp.name
FROM
(SELECT Reviewer.name, COUNT(*)
FROM Reviewer
JOIN Rating
ON Reviewer.rID = Rating.rID 
GROUP BY name
HAVING COUNT(*) >= 3) tmp
```
 
challenge (from https://github.com/yangchenyun/dbclass-exercises/blob/master/2.sql-exercise/movie-rating/core_set.sql)

```sql
select name
from Reviewer, (
  select distinct R1.rID
  from Rating R1, Rating R2, Rating R3
  where R1.rID = R2.rID and (R1.mID <> R2.mID or R1.ratingDate <> R2.ratingDate)
  and R1.rID = R3.rID and (R1.mID <> R3.mID or R1.ratingDate <> R3.ratingDate)
  and R3.rID = R2.rID and (R3.mID <> R2.mID or R3.ratingDate <> R2.ratingDate)
) ActiveUser
where Reviewer.rID = ActiveUser.rID
```

