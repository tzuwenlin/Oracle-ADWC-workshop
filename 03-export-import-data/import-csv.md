## Import CSV files to ADW

**Workshop steps**

1. Load a Local File to ADWC using SQL Developer
   - Using SQL Developer, expand your  connection and right click Tables, then click Import Data.

   ![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/12.png)

   - This will open the Data Import Wizard. Click Browse and locate your csv files.
     When you select the file you will see the file contents in the import wizard.

   ![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/13.png)

   - Click Next. In the next screen enter *your_talbe_name* as the table name you will create and load the
     data into.

   ![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/13.png)

   - Click Next. The next screen allows you to select the columns you want for this table. For this exercise
     leave the columns as-is which means the table will have all columns available.

   ![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/15.png)

   - Click Next. The next screen allows you to look at the data types for each column. You can change the
     data types if you need to. For this exercise leave the data types as default.

   ![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/16.png)

   - Click Next. The next page will display a summary for the import operation.

   ![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/17.png)

   - Click Finish to complete the import wizard and start the data load. When the data load finishes you will
     see a message saying the import was completed.

   - Your source file is now loaded into ADWC. You can run a query on the table to see your data.

     select * from your_talbe_name;
