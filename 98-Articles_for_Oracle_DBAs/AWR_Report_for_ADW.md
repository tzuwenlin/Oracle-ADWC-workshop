# How to generate AWR report from ADWC

## Reference
SRDC - How to Collect Performance Information for Oracle Autonomous Data Warehouse Cloud Guarantee (Doc ID 2383565.1)

## Requirements
Install oracle client

## Action

```sql
-- connect to adw as admin
conn admin/"YourAdminPassword"@jdxxx01_high

-- set sqlplus properties
col PDB_NAME format a25
set linesize 140

define num_days = 1

-- Get list of awr snapshot
set linesize 170
col PDB_NAME format a25
select a.pdb_name,
       a.instance_number inst_num,
       b.snap_id ,
       to_char(b.begin_interval_time,'MM/DD/YYYY:HH24:MI:SS') snap_begin_time,
       to_char(b.end_interval_time,'MM/DD/YYYY:HH24:MI:SS') snap_end_time
  from dba_hist_pdb_instance a,
       cdb_hist_snapshot b     
 where a.con_dbid = b.dbid
   and a.instance_number = b.instance_number
   and a.startup_time = b.startup_time
   and b.end_interval_time >= to_date(sysdate - &num_days)
 order by pdb_name, snap_id;
```

Result
```
PDB_NAME                    INST_NUM    SNAP_ID     SNAP_BEGIN_TIME       SNAP_END_TIME
------------------------ ----------- ---------- ------------------- -------------------
DDWGE8YWJS2POTP_JDWDB01            2         48 04/19/2019:05:00:15 04/19/2019:06:02:52
DDWGE8YWJS2POTP_JDWDB01            2         48 04/19/2019:05:00:15 04/19/2019:06:02:52
DDWGE8YWJS2POTP_JDWDB01            2         49 04/19/2019:06:02:52 04/19/2019:07:03:32
DDWGE8YWJS2POTP_JDWDB01            2         49 04/19/2019:06:02:52 04/19/2019:07:03:32
```


```sql
-- Set SQL PLUS properties
set long 100000000 longchunksize 100000000 linesize 200 head off feedback off echo off TRIMSPOOL ON TRIM ON

-- spool awr_<inst_num>_<begin_snap>_<end_snap>_adw_pdb.html
spool awr_2_48_49_atp_pdb.html

-- select output from table(dbms_workload_repository.awr_report_html((select distinct(dbid) from cdb_hist_snapshot), <inst_num>, <beginid>, <endid> ));
select output from table(dbms_workload_repository.awr_report_html((select distinct(dbid) from cdb_hist_snapshot), 2, 48, 49 ));

spool off

```

awr_2_48_49_atp_pdb.html will generate in local directory.
