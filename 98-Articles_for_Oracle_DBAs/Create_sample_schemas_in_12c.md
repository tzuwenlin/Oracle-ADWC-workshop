# Use scripts_from_oracle to create the Oracle Database 12.2.0.1 Sample Schemas.

## Requirements  

Before connect to DBCS os. You must have to finished the following steps.
* [2-Create_Database_Cloud_Service_(DBCS)](../2-Create_Database_Cloud_Service_(DBCS))
* [3-Connect_to_DBCS_using_putty](../3-Connect_to_DBCS_using_putty)

## References
https://github.com/oracle/db-sample-schemas/releases
https://github.com/oracle/db-sample-schemas/releases/tag/v12.2.0.1

## overview
1. Oracle DB 12.2 在 DBCA 勾選 sample schema 只會建立 hr schema 其他如 oe、sh 等教育訓練文件所提到的 schema 需要額外安裝。

2. Information from oracle cloud interface.
    public ip : 129.213.147.97   
    private ip : 10.0.0.3    
    Connection Strings : Easy Connect :    
    jdbcs.sub04xxx8160.jamesvcn.oraxxxvcn.com:1521/jdb01_iad1m2.sub04xxx8160.jamesvcn.oraxxxvcn.com    

3. When you create sample schemas , you must connect to PDB (Private Database) , otherwise an error will occur when create schemas.

## Action

#### List Database_listener_status to get connect string。
```bash
[oracle@jdbcs admin]$ lsnrctl status

LSNRCTL for Linux: Version 12.2.0.1.0 - Production on 24-APR-2019 01:27:09

Copyright (c) 1991, 2016, Oracle.  All rights reserved.

Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
STATUS of the LISTENER
------------------------
Alias                     LISTENER
Version                   TNSLSNR for Linux: Version 18.0.0.0.0 - Production
Start Date                23-APR-2019 02:29:45
Uptime                    0 days 22 hr. 57 min. 23 sec
Trace Level               off
Security                  ON: Local OS Authentication
SNMP                      OFF
Listener Parameter File   /u01/app/18.0.0.0/grid/network/admin/listener.ora
Listener Log File         /u01/app/grid/diag/tnslsnr/jdbcs/listener/alert/log.xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=LISTENER)))
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=10.0.0.3)(PORT=1521)))
Services Summary...
Service "+APX" has 1 instance(s).
  Instance "+APX1", status READY, has 1 handler(s) for this service...
Service "+ASM" has 1 instance(s).
  Instance "+ASM1", status READY, has 1 handler(s) for this service...
Service "+ASM_DATA" has 1 instance(s).
  Instance "+ASM1", status READY, has 1 handler(s) for this service...
Service "+ASM_RECO" has 1 instance(s).
  Instance "+ASM1", status READY, has 1 handler(s) for this service...
Service "872aa79187a118ddxxxxx0000a4518.sub041xxxx0.jamesvcn.xxxxx.com" has 1 instance(s).
  Instance "jdb01", status READY, has 2 handler(s) for this service...
Service "jdb01XDB.sub04xxx8160.jamesvcn.oraxxxvcn.com" has 1 instance(s).
  Instance "jdb01", status READY, has 1 handler(s) for this service...
Service "jdb01_iad1m2.sub04xxx8160.jamesvcn.oraxxxvcn.com" has 1 instance(s). <= "CDB 連線"
  Instance "jdb01", status READY, has 2 handler(s) for this service...
Service "jpxx0db1.sub04xxx8160.jamesvcn.oraxxxvcn.com" has 1 instance(s).       <= "PDB 連線"
  Instance "jdb01", status READY, has 2 handler(s) for this service...
The command completed successfully
```

* 根據 list 的 service list , 連線說明如下。    
jdb01_iad1m2.sub04xxx8160.jamesvcn.oraxxxvcn.com is connect to CDB.    
jpxx0db1.sub04xxx8160.jamesvcn.oraxxxvcn.com       is connect to PDB    

#### Verify available connections.

Note : In CDB database , You can't not create any normal user in CDB.

* connect to CDB
```
sqlplus system/YourSysPassowrd@10.0.0.3:1521/jdb01_iad1m2.sub04xxx8160.jamesvcn.oraxxxvcn.com

sqlplus sys/YourSysPassowrd@10.0.0.3:1521/jdb01_iad1m2.sub04xxx8160.jamesvcn.oraxxxvcn.com as sysdba

sqlplus sys/YourSysPassowrd@jdbcs.sub04xxx8160.jamesvcn.oraxxxvcn.com:1521/jdb01_iad1m2.sub04xxx8160.jamesvcn.oraxxxvcn.com as sysdba

SQL> show pdbs

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ ONLY  NO
         3 jpxx0db1                         READ WRITE NO

SQL> show con_name

CON_NAME
------------------------------
CDB$ROOT
```

* connect to PDB
```
# sqlplus sys/YourSysPassowrd@jdbcs.sub04xxx8160.jamesvcn.oraxxxvcn.com:1521/jpxx0db1.sub04xxx8160.jamesvcn.oraxxxvcn.com as sysdba


SQL> show con_name;

CON_NAME
------------------------------
jpxx0db1
```

* 下載 oracle db 12.2 sample schema       
```
sudo su - oracle
cd /tmp
wget https://github.com/oracle/db-sample-schemas/archive/v12.2.0.1.tar.gz
tar -zxvf v12.2.0.1.tar.gz
cp -a db-sample-schemas-12.2.0.1/ $ORACLE_HOME/demo
```

*  建立 schema    
1. 若為CDB、PDB架構，則會出現 ORA-65096: invalid common user or role name，因此要選擇 PDB 的 EASY CONNECT STRING.
2. 建立 HR OE .. 等 USER 會有密碼複雜性問題，範例中使用的密碼為 UserPassword1234--.
3. 注意 sys 、 system 帳號是否有 lock 住.
4. ADW ATP 沒有SYS帳號資料，無法利用此方式建立 sample schemas.

* example from oracle github

```
# https://github.com/oracle/db-sample-schemas
# Change all embedded paths to match your working directory
#    perl -p -i.bak -e 's#__SUB__CWD__#'$(pwd)'#g' *.sql */*.sql */*.dat
#    or
#    sed -i "s#__SUB__CWD__#$(pwd)#g" `grep __SUB__CWD__ -rl --include="*.sql" ./`
#
#    @?/demo/schema/mksample <SYSTEM_password> <SYS_password>
#                    <HR_password> <OE_password> <PM_password> <IX_password>
#                    <SH_password> <BI_password> EXAMPLE TEMP
#                    $ORACLE_HOME/demo/schema/log/ localhost:1521/pdb
```

* 修改腳本中變數的宣告方式
```
# cd $ORACLE_HOME/demo/db-sample-schemas-12.2.0.1
# sed -i "s#__SUB__CWD__#$(pwd)#g" `grep __SUB__CWD__ -rl --include="*.sql" ./`
```
* 使用 sys 帳號 連線到 PDB。
```
# sqlplus sys/YourSysPassowrd@jdbcs.sub04xxx8160.jamesvcn.oraxxxvcn.com:1521/jpxx0db1.sub04xxx8160.jamesvcn.oraxxxvcn.com as sysdba

SQL> show con_name;

CON_NAME
------------------------------
jpxx0db1

```

* 建立 sample schema    

```
SQL> @?/demo/db-sample-schemas-12.2.0.1/mksample YourSysPassowrd YourSysPassowrd
  UserPassword1234-- UserPassword1234-- UserPassword1234-- UserPassword1234--
  UserPassword1234-- UserPassword1234--
  users temp
  $ORACLE_HOME/demo/db-sample-schemas-12.2.0.1/log/
  jdbcs.sub04xxx8160.jamesvcn.oraxxxvcn.com:1521/jpxx0db1.sub04xxx8160.jamesvcn.oraxxxvcn.com

............. 略 .................
specify temporary tablespace for HR as parameter 3:

specify password for SYS as parameter 4:

specify log path as parameter 5:

specify connect string as parameter 6:

DROP USER hr CASCADE
          *
ERROR at line 1:
ORA-01918: user 'HR' does not exist

User created.

User altered.

............. 略 .................

SH     PRODUCTS_PROD_SUBCAT_IX               0          0
SH     PROMO_PK                              0          0
SH     SUP_TEXT_IDX
SH     TIMES_PK                              0          0

61 rows selected.

SQL>
```
