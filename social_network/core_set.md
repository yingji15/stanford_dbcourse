

 
SQL Social-Network Query Exercises Core Set [Post-Deadline Practice] 下一个 
 
Highschooler ( ID, name, grade )

English: There is a high school student with unique ID and a given first name in a certain grade.

Friend ( ID1, ID2 )

English: The student with ID1 is friends with the student with ID2. Friendship is mutual, so if (123, 456) is in the Friend table, so is (456, 123).

Likes ( ID1, ID2 )

English: The student with ID1 likes the student with ID2. Liking someone is not necessarily mutual, so if (123, 456) is in the Likes table, there is no guarantee that (456, 123) is also present.


```
Highschooler
ID	name	grade
1510	Jordan	9
1689	Gabriel	9
1381	Tiffany	9
1709	Cassandra	9
1101	Haley	10
1782	Andrew	10
1468	Kris	10
1641	Brittany	10
1247	Alexis	11
1316	Austin	11
1911	Gabriel	11
1501	Jessica	11
1304	Jordan	12
1025	John	12
1934	Kyle	12
1661	Logan	12

Friend
ID1	ID2
1510	1381
1510	1689
1689	1709
1381	1247
1709	1247
1689	1782
1782	1468
1782	1316
1782	1304
1468	1101
1468	1641
1101	1641
1247	1911
1247	1501
1911	1501
1501	1934
1316	1934
1934	1304
1304	1661
1661	1025
1381	1510
1689	1510
1709	1689
1247	1381
1247	1709
1782	1689
1468	1782
1316	1782
1304	1782
1101	1468
1641	1468
1641	1101
1911	1247
1501	1247
1501	1911
1934	1501
1934	1316
1304	1934
1661	1304
1025	1661

Likes
ID1	ID2
1689	1709
1709	1689
1782	1709
1911	1247
1247	1468
1641	1468
1316	1304
1501	1934
1934	1501
1025	1101
```

# Q1

Find the names of all students who are friends with someone named Gabriel.

```sql
SELECT DISTINCT h2.name
FROM Highschooler h1
JOIN Friend
JOIN Highschooler h2
ON h1.ID = Friend.ID1 AND h1.name = "Gabriel" AND h2.ID = Friend.ID2
```


# Q2

For every student who likes someone 2 or more grades younger than themselves, return that student's name and grade, and the name and grade of the student they like.

```sql
SELECT h1.name, h1.grade, h2.name, h2.grade
FROM Highschooler h1
JOIN Likes
JOIN Highschooler h2
ON h1.ID = Likes.ID1 AND h2.ID = Likes.ID2 AND h1.grade - h2.grade >= 2 
```


# Q3

For every pair of students who both like each other, return the name and grade of both students. Include each pair only once, with the two names in alphabetical order.

这个比较重要，当时一次没写出来

```sql
SELECT h1.name,h1.grade,h2.name, h2.grade
FROM Highschooler h1
JOIN Highschooler h2
JOIN
(SELECT L1.ID1, L1.ID2
FROM Likes L1
JOIN Likes L2
ON L1.ID1 = L2.ID2 AND L1.ID2 = L2.ID1 ) AS pair
ON h1.ID = pair.ID1 AND h2.ID = pair.ID2 AND h1.name < h2.name
```


# Q4

Find names and grades of students who only have friends in the same grade. Return the result sorted by grade, then by name within each grade.

```sql
SELECT DISTINCT Highschooler.name, Highschooler.grade
FROM Highschooler
JOIN (SELECT ID1
      FROM Friend
      WHERE ID1 NOT IN 
      (SELECT Friend.ID1
      FROM Friend
      JOIN Highschooler h1
      JOIN Highschooler h2
      ON Friend.ID1 = h1.ID AND Friend.ID2 = h2.ID AND h1.grade != h2.grade) ) AS sgrade
ON highschooler.ID = sgrade.ID1
ORDER BY grade, name
```



# Q5

Find the name and grade of all students who are liked by more than one other student.

```sql
SELECT Highschooler.name, Highschooler.grade
FROM Highschooler
JOIN (SELECT ID2, COUNT(ID1)
    FROM Likes
    GROUP BY ID2
    HAVING COUNT(ID1) > 1 ) tmp
ON Highschooler.ID = tmp.ID2
```


