## Prerequiste to connect to ADW

Now that you have created the ADW service, you will need to connect to it using your favorite tools.
The communication between desktop client applications and Autonomous Data Warehouse Cloud needs to be
encrypted and secured. The authentication to ADW using database users and passwords should be stored in a
secure/encrypted client-side software container called the Oracle Wallet. The Oracle wallet can simplify largescale
deployments that rely on password credentials for connecting to databases.
When this feature is configured, application code, batch jobs, and scripts no longer need embedded username
and password. Risk is reduced because such passwords are no longer exposed in the clear, and password
management policies are more easily enforced without changing application code whenever username or
password changes.

This workshop walks you through the steps to connect to ADW using your two most favorite database tools - Oracle
SQL Developer and Oracle SQL*Plus.

1. Sign in to the VM environment using Remote Desktop App

2. From the VM, start Google Chrome and browse to cloud.oracle.com

3. Locate the ADWC service from My Services dashboard. Click on the ADWC service hamburger icon and
select Open Service Console.

4. Download the Credentials Zip File

 - From the Service Console, click the Administration tab.
 - Click Download Client Credentials to download the wallet.

 ![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/03.png)

 - Specify a Password of your choice for the wallet, you will need this password when connecting to the
database later.
 - Click Download to download the wallet file to your client machine.

 ![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/04.png)

## Connect to ADW using Oracle SQL Developer

1. Start SQL Developer

2. Click the Create Connection icon in the Connections toolbox on the top left of the SQL Developer
homepage.

3. Fill in the connection details as below:
 - **Connection Name:** admin_low
 - **Username:** admin
 - **Password:** The password you specified during provisioning of ADW
 - **Connection Type:** Cloud PDB
 - **Configuration File:** Enter the full path for the wallet file you downloaded before, or click the Browse
   button to point to the location of the file (Hint: It will be in the Downloads folder).
 - **Keystore Password:** The password you specified when downloading the wallet from the ADW
   Service Console.
 - **Service:** There are 3 pre-configured database services for each database.Pick
**databasename_low** for this lab. For example, if you created a database named **adwfinance**
select **adwfinance_low** as the service.
![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/05.png)

 - Test your connection by clicking the Test button, if it succeeds save your connection information by
clicking Save, then connect to your database by clicking the Connect button. An entry for the new
connection appears under Connections.

## Connect to ADW using Oracle SQL*Plus

If you like to connect your favorite Oracle Client tools like SQL*Plus or Data Pump to ADW, the Oracle client
network configuration settings needs to be updated to use the Wallet.
Once the network settings are updated you can use the Oracle client utilities like sqlplus, sqlldr and impdp to
connect to ADW.

1. Locate the Wallet zip file in the Downloads folder.
The

2. The Wallet files need to be extracted into %ORACLE_HOME%\network\admin directory which in the workshop
VM is C:\12.1_client\network\admin.

3. Right-click on the zip file and click Extract All.

4. Test the network settings. Start a CMD shell from the workshop VM Desktop.

5. Enter the following command to start a sqlplus session:

  sqlplus admin/Password@ConnectString

6. Where Password is the password you entered when creating the service and ConnectString is the TNS
Names alias you used when connecting through SQL Developer.

7. On a successful connection you will get a SQL> Prompt.
