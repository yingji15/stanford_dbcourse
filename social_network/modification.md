# Q1

It's time for the seniors to graduate. Remove all 12th graders from Highschooler.

```sql
DELETE
FROM Highschooler
WHERE grade >= 12
```

# Q2

If two students A and B are friends, and A likes B but not vice-versa, remove the Likes tuple.

from https://github.com/yangchenyun/dbclass-exercises/blob/master/2.sql-exercise/social-network/modify_set.sql

```sql
delete from Likes
where ID1 in (
  select ID1 from (
    select L1.ID1, L1.ID2
    from Friend
    join Likes L1
    on Friend.ID1 = L1.ID1
    and Friend.ID2 = L1.ID2
    except
    select L1.ID1, L1.ID2
    from Likes L1
    join Likes L2
    on L1.ID1 = L2.ID2
    and L1.ID2 = L2.ID1
  )
)
```

# Q3

For all cases where A is friends with B, and B is friends with C, add a new friendship for the pair A and C. Do not add duplicate friendships, friendships that already exist, or friendships with oneself. (This one is a bit challenging; congratulations if you get it right.)

from https://github.com/yangchenyun/dbclass-exercises/blob/master/2.sql-exercise/social-network/modify_set.sql

```sql
insert into Friend
  select F1.ID1, F2.ID2
  from Friend F1
  join Friend F2
  on F1.ID2 = F2.ID1
  -- friends with oneself
  and F1.ID1 != F2.ID2
  -- already exist friendship
  except 
  select * from Friend
```

