## Import data using datapump impdp

**Prerequisite**
1. Install Oracle Instant Client with datapump tool
You can install the tools by unzipping the Oracle Instant Client the 'Tools' package to the location where you specify. See the [Instant Client home page](https://www.oracle.com/technetwork/database/database-technologies/instant-client/overview/index.html) for links to download for each platform.

2. Berfore import you need to create a data pump directory,but ADWC had been create data pump directory called data_pump_dir

3. Our datapump file is in the oracle cloud object storage,so we need to store our object storage credentials in our ADWC.
```
  SET DEFINE OFF
  BEGIN
  DBMS_CLOUD.CREATE_CREDENTIAL(
    credential_name => 'DEF_CRED_NAME',
    username => 'atpc_user@oracle.com',
    password => 'password'
  );
END;
/
```

This operation stores the credentials in the database in an encrypted format. You can use any name for the credential name. Note that this step is required only once unless your object store credentials change. Once you store the credentials you can then use the same credential name for all data loads.

**How to use impdp command to import to ADWC**

1. Full import
```
impdp admin/password@ADWC_high directory=DATA_PUMP_DIR　
credential=DEF_CRED_NAME dumpfile= https://swiftobjectstorage.us-phoenix-1.oraclecloud.com/v1/atpc/atpc_user/export%u.dmp  parallel=4 full=y transform=segment_attributes:n transform=dwcs_cvt_iots:y transform=constraint_use_default_index:y exclude=cluster,db_link
```
For the best import performance use the HIGH database service for your import connection and set the PARALLEL parameter to the number of CPUs in your Autonomous Transaction Processing as shown in the example.

2. Metadata only import
```
 impdp admin/password@ADWC_high directory=DATA_PUMP_DIR credential=DEF_CRED_NAME dumpfile= https://swiftobjectstorage.us-phoenix-1.oraclecloud.com/v1/atpc/atpc_user/export%u.dmp parallel=4 CONTENT=METADATA_ONLY transform=segment_attributes:n transform=dwcs_cvt_iots:y transform=constraint_use_default_index:y exclude=cluster,db_link,index
 ```

3. Data only import
```
 impdp admin/password@ADWC_high directory=DATA_PUMP_DIR credential=DEF_CRED_NAME  dumpfile= https://swiftobjectstorage.us-phoenix-1.oraclecloud.com/v1/atpc/atpc_user/export%u.dmp  parallel=4 CONTENT=DATA_ONLY transform=segment_attributes:n transform=dwcs_cvt_iots:y transform=constraint_use_default_index:y exclude=cluster,db_link
 ```

 4. Specify schemas metadata import

 ```
 impdp admin/password@ADWC_high directory=DATA_PUMP_DIR credential=DEF_CRED_NAME dumpfile=https://swiftobjectstorage.us-phoenix-1.oraclecloud.com/v1/atpc/atpc_user/export%u.dmp schemas=schemas_name  
 parallel=4 CONTENT=DATA_ONLY transform=segment_attributes:n transform=dwcs_cvt_iots:y transform=constraint_use_default_index:y exclude=cluster,db_link
 ```
