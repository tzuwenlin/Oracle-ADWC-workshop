## Create Oracle Autonomous Analytics

**Steps**

1. Go to Oracle cloud click on the navigation bar on the left → Service choose  Autonomous Analytics → Create Instance

2. After created OAA instance,you have to Create a Connection to Oracle Autonomous Data
   Warehouse
   - Once Oracle Autonomous Analytics starts, click on the Create button and the Connections
     selection highlighted below.

     ![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/18.png)

   - In the Create Connection Dialog, select the highlighted option for Oracle Autonomous Data
     Warehouse and continue through the guided wizard.

     ![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/19.png)

   - Open the ADW wallet's tnsnames.ora file, and choose one of the service names that you will
     use to connect to the Autonomous Data Warehouse. Here is an example connection entry for the
     *_high service:
     ```
      kzengdw1_high = (description=
     (address=(protocol=tcps)(port=1522)(host=adwc.us-e1-1.oraclecloud.com))
     (connect_data=(service_name=XXXXXX_high.adwc.oraclecloud.com))(security=
     (ssl_server_cert_dn="CN=adwc.us-e1.oraclecloud.com,OU=Oracle BMCS US,O=Oracle
     Corporation,L=Redwood City,ST=California,C=US")) )  
     ```

   -  Complete the following fields as follows:  
      New Connection Name: **connection_name**  
      Host: Your_Host (From the example above, host is adwc.us-e1-1.oraclecloud.com)  
      Port: Port (e.g. 1522)  
      Username: ADMIN  
      Password: Password (ADMIN's password that you supplied)  
      Service Name:Your_Service_Name (From the example above, the service name is
      XXXXXX_high.adwc.oracle.coud.com)  
   -  After completing the fields, click the Save button.

## Oracle Autonomous Analytics graphic layout

**Steps**

1. Before graphic layout,we need to create a View using SQL Developer
   ```
   create view **DV_SH_VIEW** as select
   ....
   ....
   ....
   ```
2. Upon success of creating a new connection to the Autonomous Data Warehouse, select the **Create
**button and select **Data Set**.

![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/20.png)

3. We will now choose the sales data to be analyzed and visualized in our first project. Select the
connection i just created named SALES_HISTORY.

![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/21.png)

4. Click on the ADMIN schema in the data warehouse.

![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/22.png)

5. Select View:DV_SH_VIEW

![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/23.png)

6. First click on the Add All Label in the left column, type a New Name for the Data Set as
SALES_HISTORY and then click the Add button.

![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/24.png)

7. Create a new project in Oracle Autonomous Analytics
- Let's create a project. Click on Create Project as shown below:

![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/25.png)

- Click on SALES_HISTORY data set (highlighted) and click on Add to Project.

![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/26.png)

- We will first start by browsing the data that's available in our data set. Click on Prepare and then click
to select the Menu option and then Data Tiles.

![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/27.png)

- Notice how easy it is to browse the data elements to see what is available for you to further explore.
After scrolling through the data, click the Visualize option once again to bring up a blank canvas.

![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/28.png)

- We will now create a very simple visualization project. Multi-select (ctrl+click) the five data elements
within SALES_HISTORY including PROD_NAME, AMOUNT_SOLD, CALENDAR_YEAR, PROD_CATEGORY,
and QUANTITY_SOLD.

- Drag the five selected columns to the middle of the screen.
Based

![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/29.png)

- Based on this data, Oracle Data Visualization Desktop will choose a default visualization. If not, choose
Scatter chart so it matches the view below.

![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/30.png)
