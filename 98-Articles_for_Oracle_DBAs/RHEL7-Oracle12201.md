# Oracle Database 12c Release 2 (12.2) Installation On RHEL/OEL/CentOS 7.x

### Requirements    
* OS minimal install

### References
1 Oracle Grid Infrastructure 12c (12.1.0.2.0) or higher supported version with Red Hat Enterprise Linux 7   
2 Oracle Database 12c (12.1.0.2.0) or higher supported version with Red Hat Enterprise Linux 7   
3 No Oracle ASMLib for Red Hat Enterprise Linux 7 is available at the time of this writing. My Oracle Support Article ID: 1089399.1   

### systemctl 用法與 CentOS/RHEL 6 比對

chkconfig name on 		systemctl enable name.service				Enables a service
chkconfig name off		systemctl disable name.service				Disables a service.
chkconfig --list name	systemctl status name.service				Checks if a service is enabled
						systemctl is-enabled name.service
chkconfig --list		systemctl list-unit-files --type service	Lists all services and checks if they are enabled.

### Action    


#### OS 基本設定

* Hostname 設定相關
```
hostnamectl set-hostname ora12cr201.localdomain

[root@ora12cr201 ~]# hostnamectl status
   Static hostname: ora12cr201.localdomain
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 114981a3e59c41088212720d9ab9105e
           Boot ID: a321b18cba5d45149a7ed5523ffe102f
    Virtualization: kvm
  Operating System: Oracle Linux Server 7.1
       CPE OS Name: cpe:/o:oracle:linux:7:1
            Kernel: Linux 3.8.13-55.1.6.el7uek.x86_64
      Architecture: x86_64
```

* 關閉 Selinux    
```
[root@ora12cr201 /]# sestatus
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          disabled
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      28

[root@localhost ~]# vi /etc/sysconfig/selinux
SELINUX=disabled
```

Reboot system and check selinux status

```
[root@ora12cr201 ~]# sestatus
SELinux status:                 disabled
```

#### 網路設定

```
[root@ora12cr201 ~]# cat /etc/sysconfig/network-scripts/ifcfg-enp0s3
TYPE="Ethernet"
BOOTPROTO=none
DEFROUTE="yes"
IPV4_FAILURE_FATAL=yes
IPV6INIT=no
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_PEERDNS="yes"
IPV6_PEERROUTES="yes"
IPV6_FAILURE_FATAL="no"
NAME="enp0s3"
UUID="92ae4543-5ba9-4b3e-8cc1-8d9bd021513d"
DEVICE="enp0s3"
ONBOOT=yes				<=
IPADDR=192.168.56.110
PREFIX=24
GATEWAY=192.168.56.1
USERS=root
NM_CONTROLLED=no  <=


[root@ora12cr201 ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:5a:91:70 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.110/24 brd 192.168.56.255 scope global enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe5a:9170/64 scope link
       valid_lft forever preferred_lft forever


[root@ora12cr201 ~]# ip route show
default via 192.168.56.1 dev enp0s3
169.254.0.0/16 dev enp0s3 scope link metric 1002
192.168.56.0/24 dev enp0s3 proto kernel scope link src 192.168.56.110
```

#### 關閉服務

* 關閉不需要的服務
systemctl disable NetworkManager.service
systemctl stop NetworkManager.service
systemctl restart network.service
systemctl stop firewalld.service  
systemctl disable firewalld.service		


* 查詢 NetworkManager  是否關閉
[root@ora12cr201 ~]# systemctl status NetworkManager.service
● NetworkManager.service - Network Manager
   Loaded: loaded (/usr/lib/systemd/system/NetworkManager.service; disabled; vendor preset: enabled)
   Active: inactive (dead)
     Docs: man:NetworkManager(8)


* 查詢 Firewall  是否關閉 Firewall Settings

[root@ora12cr201 ~]# systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; vendor preset: enabled)
   Active: inactive (dead)
     Docs: man:firewalld(1)

#### 安裝所需 OS Package      

* mount CDROM
```bash
mkdir /media/cdrom
mount /dev/sr0 /media/cdrom

# df -h | grep CDROM
Filesystem           Size  Used Avail Use% Mounted on
/dev/sr0             4.0G  4.0G     0 100% /media/cdrom
```

* CentOS 7 直接使用內建 repo，OEL、RHEL 7 類似如下設定
```bash
[CDROM]
name=RHEL $releasever-$basearch CDROM
baseurl=file:///media/cdrom
enabled=1
gpgchechk=1
gpgkey=file:///media/cdrom/RPM-GPG-KEY-oracle      
```

* yum 用法說明，關掉其他的 repo 只用 CDROM    
```bash
yum --disablerepo=\* --enablerepo=CDROM install -y net-tools perl      # for RHEL、OEL 7
yum --disablerepo=\* --enablerepo=c7-media install -y net-tools perl   # for CentOS 7
```

* 以下安裝的包由 Oracle 12c R1 Document 要求轉換為 yum 命令。
```bash
yum --disablerepo=\* --enablerepo=c7-media install -y \
binutils.x86_64 compat-libcap1.x86_64 gcc.x86_64 gcc-c++.x86_64 \
glibc.i686 glibc.x86_64 glibc-devel.i686 glibc-devel.x86_64 ksh libaio.x86_64 libaio.i686 \
libaio.i686 libaio.x86_64 libaio-devel.i686 libaio-devel.x86_64 libgcc.i686 libgcc.x86_64 \
libstdc++.i686 libstdc++.x86_64 libstdc++-devel.i686 libstdc++-devel.x86_64 libXi.i686 \
libXi.x86_64 libXtst.i686 libXtst.x86_64 make.x86_64 sysstat.x86_64 net-tools
```

* 以下的包由 RedHat Deploying Oracle RAC Database 12c Release 2 on RHEL7 中要求轉換為 yum 命令。    
```bash
yum --disablerepo=\* --enablerepo=c7-media install \
binutils compat-libcap1 compat-libstdc++-33 gcc gcc-c++ \
glibc glibc-devel ksh libgcc libstdc++ libstdc++-devel libaio libaio-devel \
libXext libXtst libX11 libXau libxcb libXi make sysstat libXmu libXt libXv \
libXxf86dga libdmx libXxf86misc libXxf86vm xorg-x11-utils xorg-x11-xauth nfs-utils smartmontools net-tools
```
    * 若不適 CentOS 請拿掉 --disablerepo=\* --enablerepo=c7-media



經驗證，上述安裝後再裝 oracle-rdbms-server-12cR1-preinstall 只有 compat-libstdc++-33
Package 要裝，因沒有必要安裝 oracle-rdbms-server-12cR1-preinstall。

如果不使用 Oracle Text 那麼可以忽略該包(Redhat 7或CentOS7 的安裝包中不含該包)，RedHat
還是建議還是安裝，該包在 RHEL optional-rpms repository 。

CentOS 7 直接 compat-libstdc++-33  使用以下命令由 base 安裝。

    *　附註 : Oracle Document ID 2254198.1.



yum install -y compat-libstdc++-33

Dependencies Resolved

==============================================================================================
 Package                       Arch             Version                  Repository      Size
==============================================================================================
Installing:
 compat-libstdc++-33           x86_64           3.2.3-72.el7             base           191 k

Transaction Summary
==============================================================================================
Install  1 Package



# 其他 package
yum --disablerepo=\* --enablerepo=c7-media install -y unzip net-tools



#######################################################
#                 系統資源與參數設定                  #
#######################################################

#### Kernel Parameter
cp -a /etc/sysctl.conf /etc/sysctl.conf.bkup
vi /etc/sysctl.conf

# 貼上內容如下
vm.swappiness = 0
vm.dirty_background_ratio = 3
vm.dirty_ratio = 80
vm.dirty_expire_centisecs = 500
vm.dirty_writeback_centisecs = 100
kernel.shmmax = 4398046511104
kernel.shmall = 1073741824
kernel.shmmni = 4096
kernel.sem = 250 32000 100 128
# fs.file-max needs to be set to at least 6815744 for Oracle Installation.
fs.file-max = 6815744
fs.aio-max-nr = 1048576
net.ipv4.ip_local_port_range = 9000 65500
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048576


#### 使用者 群組

groupadd -g 54321 oinstall
groupadd -g 54322 dba
groupadd -g 54323 oper
groupadd -g 54324 backupdba
groupadd -g 54325 dgdba
groupadd -g 54326 kmdba
groupadd -g 54327 asmdba
groupadd -g 54328 asmoper
groupadd -g 54329 asmadmin  

useradd -u 54321 -g oinstall -G dba oracle
echo oracle | passwd --stdin oracle

[root@ora12cr201 ~]# id oracle
uid=54321(oracle) gid=54321(oinstall) groups=54321(oinstall),54322(dba)



#### Shell Limits for oracle user.


vi /etc/security/limits.conf

oracle soft nproc 2047
oracle hard nproc 16384
oracle soft nofile 1024
oracle hard nofile 65536
oracle soft stack 10240
oracle hard stack 32768



vi /etc/profile

if [ $USER = "oracle" ]; then
    if [ $SHELL = "/bin/ksh" ]; then
       ulimit -u 16384
       ulimit -n 65536
    else
       ulimit -u 16384 -n 65536
    fi
fi



#### create directory for oracle software

 mkdir -p /u01/app/oracle
 chown -R oracle:oinstall /u01/app
 chmod -R 775 /u01/app

vi ~/.bash_profile

#### profile settings for oracle user
export ORACLE_HOSTNAME=ora12cr201.localdomain
# export ORACLE_UNQNAME=orcl
# export ORACLE_SID=orcl1
export ORACLE_BASE=/u01/app/oracle
export ORACLE_HOME=/u01/app/oracle/product/12.2.0/dbhome_1/
export PATH=/usr/sbin:$PATH
export PATH=$ORACLE_HOME/bin:$PATH
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib
export CLASSPATH=$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib
stty -istrip tab3 -clocal echoe
#stty erase H



#######################################################
#                 安裝 Oracle                         #
#######################################################

cd /tmp/source/oracle12cR2
unzip -q linuxx64_12201_database.zip
cd database


[oracle@ora12cr201 oracle12cR2]# who -m
root     pts/0        2019-04-04 18:28 (192.168.60.21)

export DISPLAY=192.168.60.21:0.0
./runInstaller

[oracle@ora12cr201 database]$ ./runI*
Starting Oracle Universal Installer...

Checking Temp space: must be greater than 500 MB.   Actual 45355 MB    Passed
Checking swap space: must be greater than 150 MB.   Actual 2047 MB    Passed
Checking monitor: must be configured to display at least 256 colors.    Actual 16777216    Passed
Preparing to launch Oracle Universal Installer from /tmp/OraInstall2019-04-04_06-59-02PM. Please wait ...

# Win7 要安裝 Xming server 並執行，Win7 桌面上就會出現安裝畫面。


/u01/app/oraInventory/orainstRoot.sh
/u01/app/oracle/product/12.2.0/dbhome_1/root.sh # 一直 enter















DBCA : 都差不多
	1 . 多了可以註冊到 EM grid control 選項
	2 . 多了 Create As Container database。
	3 . 內建的 Enterprise Manager 換Port了。


# 附註 1
Missing or Ignored package compat-libstdc+±33-3.2.3 causes Text Issues (文檔 ID 2254198.1)

如果不使用 Oracle Text 那麼可以忽略該包(Redhat 7或CentOS7 的安裝包中不含該包)。

APPLIES TO:
Oracle Text - Version 11.2.0.3 to 12.2 BETA1 [Release 11.2 to 12.2]
Linux x86-64

SYMPTOMS

A) While installing Oracle Database 12c, the prerequisite check will look for a package compat-libstdc+±33-3.2.3 (x86_64) , if it is not present a warning will be issued and the installation will not proceed unless the user chooses the option “Ignore All”.

OR

B) If the package compat-libstdc+±33-3.2.3 (x86_64) was ignored during installation and now, if you create an index that uses the Text filter (ctxhx) without the library being present, the index will appear to succeed, but an error will be logged for each row that failed to index in the view CTX_USER_INDEX_ERRORS. The error text (ERR_TEXT) will be:

DRG-11207: user filter command exited with status 127



The affected documents will then not be searchable as their content has not been extracted.

OR

C) Calling one of the CTX_DOC procedures will cause a similar error message as (B). For example:

ORA-20000: Oracle Text error:
DRG-11207: user filter command exited with status 127
ORA-06512: at “CTXSYS.DRUE”, line 160
ORA-06512: at “CTXSYS.CTX_DOC”, line 1473
ORA-06512: at line 1

CAUSE

As of Red Hat Enterprise Linux version 7 compat-libstdc+±33-3.2.3 (x86_64) has been moved into the optional channel. RedHat policy on this software is that support and guarantees cannot be maintained.
This policy may apply to other Linux x86_64 distributions either now or in the future. The package may also have been missed from installation in the case of Oracle Linux and the installer options to ‘ignore pre-reqs’ chosen on other 11g Release 2 or 12c.

SOLUTION

It is recommended to install package compat-libstdc+±33-3.2.3 (x86_64) if you are using Oracle Text or have plans to use it in future on the same release. Package can normally be installed/checked by running (as root) a command such as: (the exact command may vary by distribution).

rpm -qa | grep compat-libstdc+±33
yum install compat-libstdc+±33



On RedHat 7 you can add the optional channel to your yum installer with a command like:

yum-config-manager --enable rhel-7-server-optional-rpms

If you decided to not install this package, you can still proceed with the installation of Oracle by ignoring the warning and almost all features of the database will be unaffected and no errors will be reported in the database alert log.



The package is needed because it provides the library libstdc++.so.5 and this library is used only by the ctxhx executable in Oracle Text. Ctxhx is a program which is used to filter “formatted” documents such as word processor files, spreadsheets and PDF files in order to extract text to be indexed. When invoked, it runs in its own address space separate from the main Oracle executable.

It is used in just two places:

1- When creating an Oracle Text context index (indextype CTXSYS.CONTEXT) which uses the AUTO_FILTER preference. This preference may be manually specified, but is also automatically used when using FILE_DATASTORE, URL_DATASTORE or creating an index on a binary BLOB or BFILE column.

2- When using the FILTER, IFILTER or POLICY_FILTER procedures (or one of the procedures which depend on them such as HIGHLIGHT) from the CTXSYS.CTX_DOC package.

To Check whether the system is affected by this issue

The easiest way is to run the ctxhx executable directly:

Login on OS with Oracle Software owner

ORACLE@HOSTNAME>$ $ORACLE_HOME/ctx/bin/ctxhx

This would normally produce a “USAGE” message. If the necessary library is not present, you will see:
/oraclehomepath/ctx/bin/ctxhx: error while loading shared libraries: libstdc++.so.5: cannot open shared object file: No such file or directory

This issue is fixed in Oracle Database 12c Release 2 (version 12.2.0.1) which doesn’t reply on the same library but installer may still report the missing package as per Bug 25768879 : NECESSARY PACKAGES MISSING FROM 12.2 DB INSTALL PREREQ
and documentation http://docs.oracle.com/database/122/LADBI/supported-red-hat-enterprise-linux-7-distributions-for-x86-64.htm#BEGIN
REFERENCES

NOTE:2062336.1 - Installation of 12.1.0.2 Oracle Database Server/Client on RHEL 7/OL7 reports pre-requisite package ‘compat-libstdc++’ missing.
BUG:21151912 - ORACLE 12C CLIENT/DB ON RHEL 7 FAILS WITH MISSING OF COMPAT-LIBSTDC+±33-3.2.3

http://blog.itpub.net/26736162/viewspace-2214380/
