# overview

My answers to [PostgreSQL Exercises](https://pgexercises.com/).

## basic

everything from a table
```sql
select * from cd.facilities;
```

specific columns from a table
```sql
select name, membercost from cd.facilities;
```

control row retrieval pt. 1
```sql
select * from cd.facilities where membercost > 0;
```

control row retrieval pt. 2
```sql
select facid, name, membercost, monthlymaintenance 
from cd.facilities 
where membercost > 0 and (membercost < monthlymaintenance/50.0);
```

string search
```sql
select * from cd.facilities where name like '%Tennis%';
```

matching against multiple values
```sql
select * from cd.facilities where facid in (1,5);
```

classifying results
```sql
select name, 
	case when (monthlymaintenance > 100) then
		'expensive'
	else
		'cheap'
	end as cost
	from cd.facilities;  
```

working w/ dates
```sql
select memid, surname, firstname, joindate 
from cd.members
where joindate > '2012-09-01';
```

rm dupes, order
```sql
select distinct surname from cd.members order by surname limit 10;
```

combining multiple queries
```sql
select surname 
	from cd.members
union
select name
	from cd.facilities;
```

simple aggregation
```sql
select max(joindate) from cd.members;
```

more aggregation
```sql
select firstname, surname, joindate 
from cd.members 
where joindate = (
    select max(joindate) from cd.members
);
```
