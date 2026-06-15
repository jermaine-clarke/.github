# SQL Style Guidelines

The database uses the PostgreSQL dialect which is a variant of the ANSI SQL Standard
with proprietary extensions. This is heavily influenced by Simon Holywell's
[guide](https://www.sqlstyle.guide/).

## Files

Each file should contain code for a single primary database object such as a table,
function, or procedure. Files may should include the code for secondary database objects
such as indexes and triggers.


### Copyright Notice

Every file should begin with a copyright notice similar to:

```sql
/***************************************************************************************
 filename.sql
 Copyright (c) 2026. Revenue Security, Planning, Research, and Field Optimisation
 All rights reserved.
***************************************************************************************/
```

## Naming

Naming conventions are the most important for a code governance. Consistency is more
important than individual preference, so regardless of whether you find them sensible or
not, the rules should be followed.

Give things names that make their purpose or intent understandable to a new reader, even
someone on a different team than the owners. Do not worry about saving horizontal space
as it is far more important to make your code immediately understandable by a new
reader.

General guidelines may be superseded in subsequent sections:

* Ensure names do not overlap with reserved keywords.
* Names must begin with a letter and may not end with an underscore.
* Names are lower case unless otherwise specified.
* Only use letters, numbers and underscores in names.
* Avoid the use of multiple consecutive underscores—these can be hard to read.
* Use underscores to separate words `replace_spaces_with_underscores`.
* Do not use descriptive prefixes (Hungarian notation) such as `tbl_` or `proc_`
* Minimize the use of abbreviations that would likely be unknown to someone outside your
  project, especially acronyms and initialisms. Do not abbreviate by deleting letters
  within a word. Avoid abbreviations and if you have to use them make sure they are
  commonly understood. Note that certain universally-known abbreviations are OK, such as
  `id` for identifier.

### File Names

Filenames should be all lowercase and can include underscores `_` or dashes `-`. Files
should end with the extension `.sql`. Examples of acceptable file names:

    billed_consumption.sql
    my-procedure.sql
    load_table.sql

In general, make your filenames very specific. For example, use `billing_detail_log.sql`
rather than `log.sql`.

### Reserved Words

Always use uppercase for reserved keywords like `SELECT`, `WHERE`, or `AS`. Avoid the
abbreviated keywords and use the full length ones where available (prefer `ABSOLUTE` to
`ABS`).

### Tables

Prefer the use of collective over plural nouns. For example `staff` over `employees` and
`people` over `individuals`.

Never give a table the same name as one of its columns.

Avoid concatenating two table names together to create the name of a relationship table.
For example, prefer `assignment` over `employee_task`.

### Columns

Always use a singular name.

### Procedures

Must include a verb.

## Comments

### Script Comments

Include comments in SQL code where necessary. Prefer single line comments `--`

```sql
-- Updating the file record after writing to the file.
UPDATE table
SET 
  first_column = '1980-02-22 13:19:01.00000', 
  second_column = 209732
 WHERE third_column = 50;
```

### Database Object Comments

Always include comments for database objects including for each column in a table.

## Formatting

### Aliases

Always include the `AS` keyword as it makes the code clearer.

**Good**

```sql
SELECT
  SUM(energy) AS total_energy
FROM
  billing.billed_consumption
```

**Bad**

```sql
SELECT
  SUM(energy) total_energy
FROM
  billing.billed_consumption
```

### Line Length

Lines should not exceed 88 characters. Strings should be broken using implicit
concatenation.

```sql
COMMENT ON table IS 'This is a long string that has been implicitly broken up into '
    'multiple lines.'
```

### White Space

Use spaces instead of tabs with 2 spaces per indent.

#### Operators

Although not exclusive, always include spaces:
* Before and after binary operators (`=`, `&&`, `>=`, etc.)
* after commas `,`


#### Line Spacing

Always start a new line:
* before `AND` or `OR`
* after root keywords such as `SELECT`, `FROM`, and `WHERE`
* after semicolons to separate statements
* after a comma when listing columns

#### Indentation/Alignment

* Root keywords should all start on the same character boundary on their own line.
* It is acceptable to include an argument on the same line as the root keyword, if there
  is exactly one argument.
* Indent arguments or parameters for each root keyword by one level.


**Good**
```sql
SELECT
  first_name,
  last_name
FROM
  billing.customer
WHERE
  data_source_id = 3
  AND enroll_date > '20180101'
LIMIT
  10
```

**Acceptable**
```sql
SELECT
  first_name,
  last_name
FROM billing.customer
WHERE
  data_source_id = 3
  AND enroll_date > '20180101'
LIMIT 10
```

```sql
SELECT id
FROM billing.customer
WHERE
  data_source_id = 3
  AND enroll_date > '20180101'
LIMIT 1
```

**Bad**
```sql
SELECT first_name, last_name
FROM
  billing.customer
WHERE
  data_source_id = 3
  AND enroll_date > '20180101'
LIMIT
  10
```

```sql
SELECT 
  first_name, 
  last_name
FROM
  billing.customer
WHERE data_source_id = 3
  AND enroll_date > '20180101'
LIMIT
  10
```

The `ON` and `USING` keywords should start on a new line indented one level more than
the join keyword and be followed by the join conditions starting on the same line.
For example:

**Good**
```sql
SELECT
  node.id
FROM
  billing.premises
INNER JOIN
  gis.electric_node AS node
  ON node.utility_id = premises.id
WHERE
  premises.lifetime @> CURRENT_TIMESTAMP
  AND premises.geom IS NOT NULL
```

**Bad**
```sql
SELECT
  node.id
FROM
  billing.premises
INNER JOIN
  gis.electric_node AS node ON node.utility_id = premises.id
WHERE
  premises.lifetime @> CURRENT_TIMESTAMP
  AND premises.geom IS NOT NULL
```

```sql
SELECT
  node.id
FROM
  billing.premises
INNER JOIN
  gis.electric_node AS node
ON 
  node.utility_id = premises.id
WHERE
  premises.lifetime @> CURRENT_TIMESTAMP
  AND premises.geom IS NOT NULL
```

## Misc

* Make use of `BETWEEN` where possible instead of combining multiple statements with
  `AND`.
* Use `IN()` instead of multiple `OR` clauses.
* Where a value needs to be interpreted before leaving the database use the `CASE`
  expression. `CASE` statements can be nested to form more complex logical structures.
* Avoid the use of `UNION` clauses and temporary tables where possible. If the schema
  can be optimised to remove the reliance on these features then it most likely should be.
