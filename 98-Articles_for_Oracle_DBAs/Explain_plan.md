# Explain Plan


#### SQL 語句執行範例

ALTER SESSION SET STATISTICS_LEVEL=ALL;  -- 不一定需要

SELECT /*+ james text*/ e.last_name, e.first_name, d.department_name
FROM   employees e, departments d
WHERE  e.department_id=d.department_id
AND    e.last_name like 'A%';

#### 使用 user 產生預估的執行計畫

```sql
SQL>  conn oe/"YourPassword"@jdbcs.subxxx538160.jaxxxxvcn.oraclevcn.com:1521/jpxxx01.subxxx538160.jaxxxxvcn.oraclevcn.com

SQL> explain plan for
  SELECT /*+ james text*/ e.last_name, e.first_name, d.department_name
  FROM   employees e, departments d
  WHERE  e.department_id=d.department_id
  AND    e.last_name like 'A%';

SQL> set linesize 170
SQL> set pagesize 1000
SQL> select * from table(dbms_xplan.display());


PLAN_TABLE_OUTPUT
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Plan hash value: 3892127698

-----------------------------------------------------------------------------------------------------
| Id  | Operation                             | Name        | Rows  | Bytes | Cost (%CPU)| Time     |
-----------------------------------------------------------------------------------------------------
|   0 | SELECT STATEMENT                      |             |     3 |   102 |     5   (0)| 00:00:01 |
|   1 |  NESTED LOOPS                         |             |     3 |   102 |     5   (0)| 00:00:01 |
|   2 |   NESTED LOOPS                        |             |     3 |   102 |     5   (0)| 00:00:01 |
|   3 |    TABLE ACCESS BY INDEX ROWID BATCHED| EMPLOYEES   |     3 |    54 |     2   (0)| 00:00:01 |
|*  4 |     INDEX RANGE SCAN                  | EMP_NAME_IX |     3 |       |     1   (0)| 00:00:01 |
|*  5 |    INDEX UNIQUE SCAN                  | DEPT_ID_PK  |     1 |       |     0   (0)| 00:00:01 |
|   6 |   TABLE ACCESS BY INDEX ROWID         | DEPARTMENTS |     1 |    16 |     1   (0)| 00:00:01 |
-----------------------------------------------------------------------------------------------------

Predicate Information (identified by operation id):
---------------------------------------------------

   4 - access("E"."LAST_NAME" LIKE 'A%')
       filter("E"."LAST_NAME" LIKE 'A%')
   5 - access("E"."DEPARTMENT_ID"="D"."DEPARTMENT_ID")

Note
-----
   - this is an adaptive plan

24 rows selected.


```

#### 使用 system user 顯示已經執行過的執行計畫

```sql
 SQL> conn system/"YourPassword"@jdbcs.subxxx538160.jaxxxxvcn.oraclevcn.com:1521/jpxxx01.subxxx538160.jaxxxxvcn.oraclevcn.com

-- 1. 查詢 執行過的 SQL ID

SQL> col SQL_TEXT format a60
SQL> select sql_id,sql_text from v$sqlarea where sql_text like '  SELECT /*+ james text*/%';

SQL_ID        SQL_TEXT
------------- ------------------------------------------------------------
8mg7uzpndf0yy   SELECT /*+ james text*/ e.last_name, e.first_name, d.depar
              tment_name   FROM   employees e, departments d   WHERE  e.de
              partment_id=d.department_id   AND    e.last_name like 'A%'

SQL> select sql_id,child_number,sql_text from v$sql where sql_id='8mg7uzpndf0yy';

SQL_ID        CHILD_NUMBER SQL_TEXT
------------- ------------ ------------------------------------------------------------
8mg7uzpndf0yy            0   SELECT /*+ james text*/ e.last_name, e.first_name, d.depar
                           tment_name   FROM   employees e, departments d   WHERE  e.de
                           partment_id=d.department_id   AND    e.last_name like 'A%'

-- 2 使用 dbms_xplan.display_cursor() 顯示執行計畫。
 SQL>  select * from table(dbms_xplan.display_cursor('8mg7uzpndf0yy',0,'ALLSTATS LAST'));

 Plan hash value: 3385639666

------------------------------------------------------------------------------------------------
| Id  | Operation                            | Name        | E-Rows |  OMem |  1Mem | Used-Mem |
------------------------------------------------------------------------------------------------
|   0 | SELECT STATEMENT                     |             |        |       |       |          |
|*  1 |  HASH JOIN                           |             |      3 |  1476K|  1476K| 1077K (0)|
|   2 |   TABLE ACCESS BY INDEX ROWID BATCHED| EMPLOYEES   |      3 |       |       |          |
|*  3 |    INDEX RANGE SCAN                  | EMP_NAME_IX |      3 |       |       |          |
|   4 |   TABLE ACCESS FULL                  | DEPARTMENTS |      1 |       |       |          |
------------------------------------------------------------------------------------------------

Predicate Information (identified by operation id):
---------------------------------------------------

   1 - access("E"."DEPARTMENT_ID"="D"."DEPARTMENT_ID")
   3 - access("E"."LAST_NAME" LIKE 'A%')
       filter("E"."LAST_NAME" LIKE 'A%')

Note
-----
   - this is an adaptive plan
   - Warning: basic plan statistics not available. These are only collected when:
       * hint 'gather_plan_statistics' is used for the statement or
       * parameter 'statistics_level' is set to 'ALL', at session or system level


32 rows selected.

-- 說明
    Starts : 表示當前步驟的執行次數
    E-Rows : 當前步驟 預估 的 ROW 筆數
    A-Rows : 當前步驟 實際 的 ROW 筆數   
    		若差異過大表統計訊息不準確或 WHERE
    Buffers:


```

#### 其他 system 可用的 select view
```sql
SQL>  
select
   sql_id,
--   sql_text,
   disk_reads,
   SORTS,
   VERSION_COUNT ver_count,
-- FIRST_LOAD_TIME,
   BUFFER_GETS,
   USER_IO_WAIT_TIME UIO_WAIT_TIME,
   OPTIMIZER_COST OPT_COST,
   CPU_TIME,
   ELAPSED_TIME,
   PHYSICAL_READ_REQUESTS PR_REQ,
   PHYSICAL_WRITE_REQUESTS PW_REQ
from
   v$sqlarea
where
   sql_text like '  SELECT /*+ james text*/%'
order by
   disk_reads desc;

   SQL_ID        DISK_READS      SORTS  VER_COUNT BUFFER_GETS UIO_WAIT_TIME   OPT_COST   CPU_TIME ELAPSED_TIME     PR_REQ     PW_REQ
   ------------- ---------- ---------- ---------- ----------- ------------- ---------- ---------- ------------ ---------- ----------
   8mg7uzpndf0yy          0          0          2          27             0          5      11313        13122          0          0

SQL> col OPERATION format a20
SQL> col OPTIONS format a30
SQL> col OBJECT_NAME format a30

SQL> select operation,options,object_name,cost,id,parent_id
  from v$sql_plan
 where sql_id='8mg7uzpndf0yy'
   and child_number=0;

   OPERATION            OPTIONS                        OBJECT_NAME                          COST         ID  PARENT_ID
   -------------------- ------------------------------ ------------------------------ ---------- ---------- ----------
   SELECT STATEMENT                                                                            5          0
   HASH JOIN                                                                                   5          1          0
   NESTED LOOPS                                                                                5          2          1
   NESTED LOOPS                                                                                5          3          2
   STATISTICS COLLECTOR                                                                                   4          3
   TABLE ACCESS         BY INDEX ROWID BATCHED         EMPLOYEES                               2          5          4
   INDEX                RANGE SCAN                     EMP_NAME_IX                             1          6          5
   INDEX                UNIQUE SCAN                    DEPT_ID_PK                              0          7          3
   TABLE ACCESS         BY INDEX ROWID                 DEPARTMENTS                             1          8          2
   TABLE ACCESS         FULL                           DEPARTMENTS                             1          9          1


```


#### Delete all execution plans and buffer cache.
    -- login as user sys
    alter system flush shared_pool;
    alter system flush buffer_cache;
