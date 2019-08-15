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
select starttime
	from 
		cd.bookings as b
		inner join 
        cd.members as m
		on b.memid = m.memid
	where 
		m.surname = 'Farrell'
		and m.firstname = 'David';
```
