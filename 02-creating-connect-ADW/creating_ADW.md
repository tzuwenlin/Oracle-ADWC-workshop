## Create ADW steps

 1. Go to Oracle cloud click on the navigation bar on the left → Service choose Autonomous Data Warehouse → Create Autonomous Data Warehouse

 ![image](https://github.com/Lhanber/Oracle-ADWC-workshop/blob/master/image/02.png)

 2. Enter the required information

- Display Name : Enter a name for your ADW instance
- Database Name : Enter a database name

  The name must contain only letters and numbers, starting with a letter, 14 characters max.
- CPU Core Count : 1
- Storage (TB) : 1
- Administrator Credentials : Enter a password that meets the following requirements:

  a.The password must be between 12 and 30 characters long

  b.The password must include at least one uppercase letter, one lowercase letter, and one numeric
    character

  c.The password cannot contain the keyword ADMIN

  d.The password cannot contain the double quote (") character
- Select License Included.

Note: Ensure that you remember this password (or note it down) as you would be needing it later.
- Once you entered all the information, click Create Autonomous Data Warehouse.

 3. On the ADW Cloud Console dashboard, you will see the service that was just submitted, and the State will initially be Provisioning,the State will soon change to Available (in about 30-60 seconds).
