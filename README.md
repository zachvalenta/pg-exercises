# overview

My answers to [PostgreSQL Exercises](https://pgexercises.com/).

## basic

[everything from a table](https://pgexercises.com/questions/basic/selectall.html)
```sql
select * from cd.facilities;
```

[specific columns from a table](https://pgexercises.com/questions/basic/selectspecific.html)
```sql
select name, membercost from cd.facilities;
```

[control row retrieval pt. 1](https://pgexercises.com/questions/basic/where.html)
```sql
select * from cd.facilities where membercost > 0;
```

[control row retrieval pt. 2](https://pgexercises.com/questions/basic/where2.html)
```sql
select facid, name, membercost, monthlymaintenance 
from cd.facilities 
where membercost > 0 and (membercost < monthlymaintenance/50.0);
```

[string search](https://pgexercises.com/questions/basic/where3.html)
```sql
select * from cd.facilities where name like '%Tennis%';
```

[matching against multiple values](https://pgexercises.com/questions/basic/where4.html)
```sql
select * from cd.facilities where facid in (1,5);
```

[classifying results](https://pgexercises.com/questions/basic/classify.html)
```sql
select name, 
	case when (monthlymaintenance > 100) then
		'expensive'
	else
		'cheap'
	end as cost
	from cd.facilities;  
```

[working w/ dates](https://pgexercises.com/questions/basic/date.html)
```sql
select memid, surname, firstname, joindate 
from cd.members
where joindate > '2012-09-01';
```

[rm dupes, order](https://pgexercises.com/questions/basic/unique.html)
```sql
select distinct surname from cd.members order by surname limit 10;
```

[combining multiple queries](https://pgexercises.com/questions/basic/union.html)
```sql
select surname 
	from cd.members
union
select name
	from cd.facilities;
```

[simple aggregation](https://pgexercises.com/questions/basic/agg.html)
```sql
select max(joindate) from cd.members;
```

[more aggregation](https://pgexercises.com/questions/basic/agg2.html)
```sql
select firstname, surname, joindate 
from cd.members 
where joindate = (
    select max(joindate) from cd.members
);
```

## joins

[simple join](https://pgexercises.com/questions/joins/simplejoin.html)
```sql
select b.starttime
	from 
		cd.bookings as b
		inner join 
        cd.members as m
		on b.memid = m.memid
	where 
		m.surname = 'Farrell'
		and m.firstname = 'David';
```

[simple join 2](https://pgexercises.com/questions/joins/simplejoin2.html)
```sql
select b.starttime, f.name
    from 
        cd.bookings as b
        inner join cd.facilities as f
        on b.facid = f.facid
    where 
        f.facid in (0,1)
        and date(b.starttime) in ('2012-09-21')
        /*
            can also do range via:

            date(b.starttime) >= '2012-09-21'
            and date(b.starttime) < '2012-09-21'

            two things I don't understand yet:

            * `> 09-20` (vs. `>= 09.21`) doesn't work; presume translates as 'greater than 09-20 00:00' i.e. everything on 09.20
            * can't do equality i.e. `where date(b.starttime) = '2012-09-21'`
            - 
        */
order by b.starttime;
```

[self join - all members who have recommended another member](https://pgexercises.com/questions/joins/self.html)
```sql
select distinct m1.firstname, m1.surname
	from 
		cd.members as m1
		inner join
		cd.members as m2
		on m2.recommendedby = m1.memid
order by m1.surname, m1.firstname;
```

[self join - all members, including whoever recommended them](https://pgexercises.com/questions/joins/self2.html) --> [same question using subquery](https://pgexercises.com/questions/joins/sub.html)
```sql
select m1.firstname, m1.surname, m2.firstname, m2.surname
	from 
		cd.members m1
		left outer join 
        cd.members m2
        -- this is where thinking of joins as nested for loops makes sense
        -- seems like we're saying here is "ok, loop through every record from the join table and return on a match"
        on m1.recommendedby = m2.memid
order by m1.surname, m1.firstname;
```

[join across three tables - all members who have used tennis court](https://pgexercises.com/questions/joins/threejoin.html)
```sql
select distinct mem.firstname || ' ' || mem.surname as member, fac.name
    from 
        cd.members mem
        inner join
        cd.bookings book
        on mem.memid = book.memid
        inner join
        cd.facilities fac
        on book.facid = fac.facid
    where book.facid in (0,1) 
    -- where fac.name like '%Tennis%' -> also returns corrent result and consistent w/ string search exercise
    -- ⤵️ also feel like this doesn't need to use three tables; the where clause in itself should be sufficient
    -- not necessary as a where clause; instead, think of the final join as just a way to pull in the facilities table data necessary for our result set
order by member
```

[bookings on specific day which cost more than $30](https://pgexercises.com/questions/joins/threejoin2.html)
```sql
SELECT 
    mem.firstname || ' ' || mem.surname as member,
    fac.name as facility,
    CASE
        WHEN mem.memid = 0 THEN
            book.slots*fac.guestcost
        ELSE
            book.slots*fac.membercost
    END AS cost
    FROM
        cd.members mem
        INNER JOIN
        cd.bookings book
        ON mem.memid = book.memid
        INNER JOIN
        cd.facilities fac
        ON book.facid = fac.facid
    WHERE 
        DATE(book.starttime) IN ('2012-09-14') AND
        (
            (mem.memid = 0 AND book.slots*fac.guestcost > 30) 
            OR
            (mem.memid != 0 AND book.slots*fac.membercost > 30)
        )
```

[all members and their recommender, with no joins](https://pgexercises.com/questions/joins/sub.html)
```sql
SELECT DISTINCT
    mem.firstname || ' ' || mem.surname AS member,
    (
        SELECT rec.firstname || ' ' || rec.surname AS recommender
        FROM cd.members AS rec
        -- original approach was subquery at where clause in main query 
        -- incorrect bc `rec` needs to be in result set i.e. cannot just feed into where clause
        -- never seen subquery in the SELECT
	  	-- ❓ names declared inside subquery not available to main query
        WHERE rec.memid = mem.recommendedby
    )
FROM
    cd.members mem
ORDER BY member
```
