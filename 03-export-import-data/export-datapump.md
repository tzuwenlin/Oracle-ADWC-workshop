## Export data using datapump expdp

**Prerequisite**
1. Install Oracle Instant Client with datapump tool
You can install the tools by unzipping the Oracle Instant Client the 'Tools' package to the location where you specify. See the [Instant Client home page](https://www.oracle.com/technetwork/database/database-technologies/instant-client/overview/index.html) for links to download for each platform.

**How to use expdp command**

Berfore export you need to create a data pump directory.

`create directory data_pump_dir as '/PATH';`

1.Source database full datapump

``expdp system/manager DIRECTORY=data_pump_dir DUMPFILE=full.dmp FULL=y;``

2.The following example exports the SH schema from a source Oracle Database for migration to an Autonomous Data Warehouse database with 16 CPUs:

`expdp sh/sh@orcl \
exclude=index, cluster, indextype, materialized_view, materialized_view_log, materialized_zonemap, db_link \
data_options=group_partition_table_data  \
parallel=16 \
schemas=sh \
dumpfile=export%u.dmp`

Oracle Data Pump Export provides several export modes, Oracle recommends using the schema mode for migrating to Autonomous Data Warehouse. You can list the schemas you want to export by using the schemas parameter.

For a faster migration, export your schemas into multiple Data Pump files and use parallelism. You can specify the dump file name format you want to use with the dumpfile parameter. Set the parallel parameter to at least the number of CPUs you have in your Autonomous Data Warehouse database.

The exclude and data_options parameters ensure that the object types not required in Autonomous Data Warehouse are not exported and table partitions are grouped together so that they can be imported faster during the import to Autonomous Data Warehouse. If you want to migrate your existing indexes, materialized views, and materialized view logs to Autonomous Data Warehouse and manage them manually, you can remove those object types from the exclude list which will export those object types too. Similarly, if you want to migrate your existing partitioned tables as-is without converting them into non-partitioned tables and manage them manually you can remove the data_options argument which will export your partitioned tables as-is. For more information, see Managing Partitions, Indexes, and Materialized Views .

You can use other Data Pump Export parameters, like compression, depending on your requirements. For more information on Oracle Data Pump Export see [Oracle Database Utilities](https://docs.oracle.com/en/database/oracle/oracle-database/18/sutil/oracle-data-pump-export-utility.html#GUID-5F7380CE-A619-4042-8D13-1F7DDE429991).
