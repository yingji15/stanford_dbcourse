
social network challenge set

# Q1

Find all students who do not appear in the Likes table (as a student who likes or is liked) and return their names and grades. Sort by grade, then by name within each grade.

```sql
SELECT name,grade
FROM Highschooler
WHERE ID NOT IN  (SELECT ID1 AS ID
      FROM Likes
      UNION
      SELECT ID2 AS ID
      FROM Likes ) 
ORDER BY name, grade
```

# Q2

For each student A who likes a student B where the two are not friends, find if they have a friend C in common (who can introduce them!). For all such trios, return the name and grade of A, B, and C.

```sql
SELECT H1.name, H1.grade, H2.name, H2.grade, H3.name, H3.grade
FROM Highschooler H1
JOIN Highschooler H2
JOIN Highschooler H3
JOIN Friend F1
JOIN Friend F2, (
  SELECT * FROM Likes
  WHERE ID1, ID2 NOT IN 
  (SELECT Likes.ID1, Likes.ID2 -- A likes B and A/B are friends
  from Likes
  JOIN Friend
  ON Friend.ID1 = Likes.ID1 AND Friend.ID2 = Likes.ID2)
) as LikeNotFriend
ON F1.ID1 = LikeNotFriend.ID1 -- A, f1.ID1 = A
and F2.ID1 = LikeNotFriend.ID2 -- B, f2.ID1 = B
and F1.ID2 = F2.ID2 -- has a shared friend C, f1.ID2 = f2.ID2 = C
and H1.ID = LikeNotFriend.ID1 
and H2.ID = LikeNotFriend.ID2
and H3.ID = F2.ID2 
```

# Q3

Find the difference between the number of students in the school and the number of different first names.

```sql
select count(ID) - count(distinct name)
from Highschooler
```

# Q4

What is the average number of friends per student? (Your result should be just one number.

```sql
select avg(count)
from (
  select count(ID2) as count
  from Friend
  group by ID1
) as FriendCount
```

# Q5

Find the number of students who are either friends with Cassandra or are friends of friends of Cassandra. Do not count Cassandra, even though technically she is a friend of a friend.

```sql
SELECT COUNT(DISTINCT f1.ID2)+ COUNT(DISTINCT f2.ID2)
FROM Friend f1
JOIN Friend f2
ON f1.ID1 = (SELECT ID 
            FROM Highschooler
           WHERE name = "Cassandra" ) AND f1.ID2 = f2.ID1 AND f2.ID2 NOT IN (SELECT ID 
                                                                            FROM Highschooler
                                                                           WHERE name = "Cassandra" )
```

# Q6

Find the name and grade of the student(s) with the greatest number of friends.

```sql
SELECT name, grade
FROM
(SELECT Highschooler.ID, Highschooler.name, Highschooler.grade, COUNT(DISTINCT Friend.ID2) as cnt
FROM Friend
JOIN Highschooler
ON Friend.ID1 = Highschooler.ID 
GROUP BY Highschooler.ID
ORDER BY COUNT(DISTINCT Friend.ID2) DESC
LIMIT 1)                                                                            
```

