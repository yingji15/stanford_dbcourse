

 
Extra Problems
 

Movie ( mID, title, year, director )

English: There is a movie with ID number mID, a title, a release year, and a director.

Reviewer ( rID, name )

English: The reviewer with ID number rID has a certain name.

Rating ( rID, mID, stars, ratingDate )

English: The reviewer rID gave the movie mID a number of stars rating (1-5) on a certain ratingDate.



# Q1

Find the names of all reviewers who rated Gone with the Wind.

```sql
SELECT DISTINCT Reviewer.name
FROM Reviewer
JOIN Movie
JOIN Rating
ON Rating.mID = Movie.mID AND Reviewer.rID = Rating.rID AND Movie.title = "Gone with the Wind"
```
 
# Q2

For any rating where the reviewer is the same as the director of the movie, return the reviewer name, movie title, and number of stars.

```sql
SELECT DISTINCT Reviewer.name, Movie.title, Rating.stars
FROM Reviewer
JOIN Movie
JOIN Rating
ON Reviewer.name = Movie.director AND Reviewer.rID = Rating.rID AND Movie.mID = Rating.mID
```

# Q3

Return all reviewer names and movie names together in a single list, alphabetized. (Sorting by the first name of the reviewer and first word in the title is fine; no need for special processing on last names or removing "The".)

```sql
SELECT name
FROM
(SELECT Reviewer.name
FROM Reviewer
UNION 
SELECT Movie.title as name
FROM Movie) AS name
ORDER BY name ASC
```



# Q4

Find the titles of all movies not reviewed by Chris Jackson.

```sql
SELECT DISTINCT Movie.title 
FROM Movie 
WHERE title NOT IN 
(SELECT DISTINCT Movie.title
FROM Movie
JOIN Reviewer
JOIN Rating
ON Movie.mID = Rating.mID AND Reviewer.rID = Rating.rID AND Reviewer.name = "Chris Jackson" ) 
```



# Q5

For all pairs of reviewers such that both reviewers gave a rating to the same movie, return the names of both reviewers. Eliminate duplicates, don't pair reviewers with themselves, and include each pair only once. For each pair, return the names in the pair in alphabetical order.

```sql
SELECT DISTINCT R1.name, R2.name
FROM Reviewer R1
JOIN Reviewer R2
JOIN Rating AS Ra1
JOIN Rating AS Ra2
ON R1.rID = Ra1.rID AND R2.rID = Ra2.rID AND Ra1.mID = Ra2.mID AND Ra1.rID != Ra2.rID
WHERE R1.name < R2.name
ORDER BY R1.name, R2.name
```
 

# Q6

For each rating that is the lowest (fewest stars) currently in the database, return the reviewer name, movie title, and number of stars.

```sql
SELECT Reviewer.name, Movie.title,Rating.stars
FROM Movie
JOIN Reviewer
JOIN Rating
ON Movie.mID = Rating.mID AND Rating.rID = Reviewer.rID AND Rating.stars = (SELECT MIN(stars) FROM Rating)
```

