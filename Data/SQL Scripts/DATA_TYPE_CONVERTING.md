## Converting CHAR to DATE
A Teradata exemple:
``` js
Table: TABLE_A
Column CHAR_COL type CHAR(6)
SQL commands: CAST ; CONCAT (||) ; SUBSTRING

From CHAR_COL = '260224' to this result '2024-02-26'
```
#### The transformation query 
``` sql
SELECT 
CAST(
SUBSTRING (CHAR_COL, 1,2) ||'-'|| SUBSTRING (CHAR_COL, 3,2) ||'-'|| SUBSTRING (CHAR_COL, 5,2) 
AS DATE FORMAT 'DD/MM/YY')
FROM TABLE_A;
```
