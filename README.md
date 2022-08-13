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


