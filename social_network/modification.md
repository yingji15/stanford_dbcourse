# Q1

```sql
DELETE
FROM Highschooler
WHERE grade >= 12
```

# Q2

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
