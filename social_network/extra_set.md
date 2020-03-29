

 SQL Social-Network Query Exercises Extra Practice  SQL Social-Network Query Exercises Extra Practice
 
Students at your hometown high school have decided to organize their social network using databases. So far, they have collected information about sixteen students in four grades, 9-12. Here's the schema:

Highschooler ( ID, name, grade )

English: There is a high school student with unique ID and a given first name in a certain grade.

Friend ( ID1, ID2 )

English: The student with ID1 is friends with the student with ID2. Friendship is mutual, so if (123, 456) is in the Friend table, so is (456, 123).

Likes ( ID1, ID2 )

English: The student with ID1 likes the student with ID2. Liking someone is not necessarily mutual, so if (123, 456) is in the Likes table, there is no guarantee that (456, 123) is also present.





# Q1

For every situation where student A likes student B, but we have no information about whom B likes (that is, B does not appear as an ID1 in the Likes table), return A and B's names and grades.

 

```sql
SELECT h1.name, h1.grade, h2.name, h2.grade
FROM Likes
JOIN Highschooler h1
JOIN Highschooler h2
ON Likes.ID1 = h1.ID AND Likes.ID2 = h2.ID AND Likes.ID2 NOT IN (SELECT ID1 FROM Likes) 
```


# Q2

For every situation where student A likes student B, but student B likes a different student C, return the names and grades of A, B, and C.


```sql
SELECT h1.name, h1.grade, h2.name, h2.grade, h3.name, h3.grade
FROM Likes L1
JOIN Likes L2
JOIN Highschooler h1
JOIN Highschooler h2
JOIN Highschooler h3
ON L1.ID1 = h1.ID AND L1.ID2 = h2.ID AND L2.ID1 = L1.ID2 AND L2.ID2 != L1.ID1 AND L2.ID2 = h3.ID
```


# Q3

Find those students for whom all of their friends are in different grades from themselves. Return the students' names and grades.

```sql
SELECT name, grade
FROM Highschooler
WHERE ID IN (SELECT ID1 
       FROM Friend
       WHERE ID1 NOT IN (SELECT DISTINCT Friend.ID1
                          FROM Friend
                          JOIN Highschooler h1
                          JOIN Highschooler h2
                          ON Friend.ID1 = h1.ID AND Friend.ID2 = h2.ID AND h1.grade = h2.grade ) )
```
