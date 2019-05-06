# Install Oracle 12c client software in silent mode.

## Reference     
HOW TO INSTALL 11.2 /12.1/12.2 DATABASE/CLIENT SOFTWARE IN SILENT MODE WITH/WITHOUT USING RESPONSE FILE (DOC ID 885643.1)


## Action


### download oracle 12c client software    
```bash
wget https://download.oracle.com/otn/linux/oracle12c/122010/linuxx64_12201_client.zip?AuthParam=155676238xxxxxxxxxxxx34fd758d
```

AuthParam 可以先在 chrome 上下載一次，就會產稱一組 key 驗證碼，在 linux 就可以使用這組驗證碼 download.


### 安裝所需 OS Package      

* 以下安裝的包由 Oracle 12c R1 Document 要求轉換為 yum 命令。
```bash
yum install -y \
binutils.x86_64 compat-libcap1.x86_64 gcc.x86_64 gcc-c++.x86_64 \
glibc.i686 glibc.x86_64 glibc-devel.i686 glibc-devel.x86_64 ksh libaio.x86_64 libaio.i686 \
libaio.i686 libaio.x86_64 libaio-devel.i686 libaio-devel.x86_64 libgcc.i686 libgcc.x86_64 \
libstdc++.i686 libstdc++.x86_64 libstdc++-devel.i686 libstdc++-devel.x86_64 libXi.i686 \
libXi.x86_64 libXtst.i686 libXtst.x86_64 make.x86_64 sysstat.x86_64 \
compat-libstdc++-33 unzip net-tools
```

* 以下的包由 RedHat Deploying Oracle RAC Database 12c Release 2 on RHEL7 中要求轉換為 yum 命令。    
```bash
yum install -y \
binutils compat-libcap1 compat-libstdc++-33 gcc gcc-c++ \
glibc glibc-devel ksh libgcc libstdc++ libstdc++-devel libaio libaio-devel \
libXext libXtst libX11 libXau libxcb libXi make sysstat libXmu libXt libXv \
libXxf86dga libdmx libXxf86misc libXxf86vm xorg-x11-utils xorg-x11-xauth nfs-utils smartmontools \
compat-libstdc++-33 unzip net-tools
```

### create directory for oracle software
```bash
 mkdir -p /u01/app/oracle
 chown -R oracle:oinstall /u01/app
 chmod -R 775 /u01/app
```


### profile settings for oracle user
```bash
vi ~/.bash_profile

export ORACLE_BASE=/u01/app/oracle
export ORACLE_HOME=/u01/app/oracle/product/12.2.0/client_home1
export PATH=/usr/sbin:$PATH
export PATH=$ORACLE_HOME/bin:$PATH
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib
export CLASSPATH=$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib
stty -istrip tab3 -clocal echoe
stty erase ^H
```


### Install client software without using response file.    
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
