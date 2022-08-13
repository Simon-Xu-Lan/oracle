# index
```sql
create index brick_colour_i 
  on bricks ( colour );
 
drop index brick_shape_i;

-- Multi-column Indexes
create index brick_colour_weight_i 
  on bricks ( colour, weight ) ;

-- view indexes
select * from user_indexes
where  table_name = 'BRICKS';

-- It's possible to list up to 32 columns in an index. This query returns the indexes on BRICKS and a list of their columns:
select index_name, column_name, column_position 
from   user_ind_columns
where  table_name = 'BRICKS'
order  by index_name, column_position;
```

# Execution Plan
## Get a Basic Plan with DBMS_XPlan
- Run this to get the execution plan for the join:

```sql
select *
from   bricks b
join   colours c
on     b.colour = c.colour;

select * 
from   table(dbms_xplan.display_cursor(:LIVESQL_LAST_SQL_ID));
```
The first argument of the DISPLAY_CURSOR is the SQL ID for the statement. LIVESQL_LAST_SQL_ID is a bind variable specific to Live SQL. Only use this in Live SQL. Normally you'll want to pass NULL or a SQL ID instead.

Passing NULL gets the plan for the last SQL statement run in this session. Using a SQL ID searches for plans in the cursor cache for that statement.

## Find the SQL ID for a Statement
- To get the SQL ID for a statement, search for it in v$sql:

```sql
select sql_id, sql_text
from   v$sql
where  sql_text like 'select *%bricks b%'
/* exclude this query */
and    sql_text not like '%not this%';

```

- The SQL ID is a hash of the statement's text. This means that changes to the formatting of a SQL statement will give it a new SQL ID, even though its meaning is identical. For example the only difference between these queries is the case of select. But they have different SQL IDs!

```sql
select *
from   bricks b
join   colours c
on     b.colour = c.colour;

SELECT *
from   bricks b
join   colours c
on     b.colour = c.colour;

select sql_id, sql_text
from   v$sql
where  sql_text like '%bricks%join%colours%'
/* exclude this query */
and    sql_text not like '%not this%';
```

## Using SQL ID in DBMS_XPlan
- If you know the SQL ID for a statement, you can pass it directly to DBMS_XPlan:

```sql
select *
from   bricks b
join   colours c
on     b.colour = c.colour;

select * 
from   table(dbms_xplan.display_cursor('1jqgaskqzc3tq'));
```

- If you don't know the SQL ID, but have the statement's text, you can lookup its SQL ID and pass it to DBMS_XPlan in one statement with this query:
- The "statement's text" is 
```sql
select /* my statement text */* from colours;

select p.*  
from   v$sql s, table (  
  dbms_xplan.display_cursor (  
    s.sql_id, s.child_number, 'BASIC'  
  )  
) p  
where s.sql_text like '%my statement text%'  /* enter text from the target statement here */
and   s.sql_text not like '%not this%';
```



