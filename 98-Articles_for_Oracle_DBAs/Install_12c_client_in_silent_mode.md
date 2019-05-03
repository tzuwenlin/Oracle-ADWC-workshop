# Install Oracle 12c client software in silent mode.

### Reference     
HOW TO INSTALL 11.2 /12.1/12.2 DATABASE/CLIENT SOFTWARE IN SILENT MODE WITH/WITHOUT USING RESPONSE FILE (DOC ID 885643.1)


### Action


#### download oracle 12c client software    
```bash
wget https://download.oracle.com/otn/linux/oracle12c/122010/linuxx64_12201_client.zip?AuthParam=155676238xxxxxxxxxxxx34fd758d
```

AuthParam 可以先在 chrome 上下載一次，就會產稱一組 key 驗證碼，在 linux 就可以使用這組驗證碼 download.

#### Install client software without using response file.    
```bash
./runInstaller -silent  -force \
  INVENTORY_LOCATION=/u01/app/oracle/oraInventory \    
  FROM_LOCATION=/home/oracle/client/stage/products.xml \    
  UNIX_GROUP_NAME=oinstall \    
  ORACLE_HOME=/u01/app/oracle/product/12.2.0/client_home1 \    
  ORACLE_HOME_NAME="client_home1" \    
  ORACLE_BASE=/u01/app/oracle oracle.install.client.installType="Administrator"
```

* 完成會要求執行 root.sh    
    /u01/app/oracle/product/12.2.0/client_home1/root.sh
