<!-- Pagination for list
--- -->

# 1. Problem

A collection should support pagination, even if result are typically small

> Rationale: If an API does not support pagination from the start, supporting it later is troublesome because adding pagination breaks the API's behavior. Clients that are unaware that the API now uses pagination could incorrectly assume that they received a complete result, when in fact they only received the first page.
> 
> source: [https://cloud.google.com/apis/design/design_patterns#list_pagination](https://cloud.google.com/apis/design/design_patterns#list_pagination)

# 2. Expected result

* Easy to implement
* need only 2 parameters: number of item per page and page number

# 3. Solution design

## Database level

Each database technology will have a different implementation and syntax for pagination.

MySQL

```sql
SELECT * FROM your_table
LIMIT start_row OFFSET max_number_of_row;
```

SQL Server
```sql
SELECT * FROM your_table
OFFSET number_of_skipped_row ROWS
FETCH NEXT max_number_of_row ROWS ONLY;
```

There are other ways to pagination in datbase level likes using `TOP` clause in SQL Server. We choose the proposed syntax for theirs performances and simplicity. This is useful when developer need to squeeze every milliseconds.

## Application level

