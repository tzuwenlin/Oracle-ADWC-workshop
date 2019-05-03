
# 12c Default spfile parameter

#### Overview
https://docs.oracle.com/database/121/ADMQS/GUID-157FFD6E-04B0-46D8-A6C9-A9362E5D717D.htm

The Database Configuration Assistant (DBCA) Database Template window enables you to select the type of database template to use to create the database. You can select:
* General Purpose or Transaction Processing
* Custom Database
* Data Warehouse

dbca 建立資料庫有三個類型的 template 可選。

Data Warehouse 多了一個參數
```
*.star_transformation_enabled='TRUE'
```

#### 自行安裝 12.2.0.1 預設 spfile 內容 (DW)
```
dw01cdb.__data_transfer_cache_size=0
dw01cdb.__db_cache_size=1107296256
dw01cdb.__inmemory_ext_roarea=0
dw01cdb.__inmemory_ext_rwarea=0
dw01cdb.__java_pool_size=16777216
dw01cdb.__large_pool_size=33554432
dw01cdb.__oracle_base='/u01/app/oracle'#ORACLE_BASE set from environment
dw01cdb.__pga_aggregate_target=536870912
dw01cdb.__sga_target=1610612736
dw01cdb.__shared_io_pool_size=83886080
dw01cdb.__shared_pool_size=352321536
dw01cdb.__streams_pool_size=0
*.audit_file_dest='/u01/app/oracle/admin/dw01cdb/adump'
*.audit_trail='db'
*.compatible='12.2.0'
*.control_files='/u01/app/oracle/oradata/dw01cdb/control01.ctl','/u01/app/oracle/oradata/dw01cdb/control02.ctl'
*.db_block_size=8192
*.db_domain='localdomain'
*.db_name='dw01cdb'
*.diagnostic_dest='/u01/app/oracle'
*.dispatchers='(PROTOCOL=TCP) (SERVICE=dw01cdbXDB)'
*.enable_pluggable_database=true
*.local_listener='LISTENER_DW01CDB'
*.nls_language='AMERICAN'
*.nls_territory='AMERICA'
*.open_cursors=300
*.pga_aggregate_target=512m
*.processes=300
*.remote_login_passwordfile='EXCLUSIVE'
*.sga_target=1536m
*.star_transformation_enabled='TRUE'
*.undo_tablespace='UNDOTBS1'
```
