A common way to generate monthly expense graphic reports is by using LAMP stack and writing large amounts of code in either java, python, PHP, or Perl etc to query the database and to draw the graphical reports.
In this project, I will be using cutting-edge softwares, Grafana and MariaDB, to make the process simpler and easier.
Here is a screenshot of expense report dashboard.
<img src=https://github.com/Miker48/Expense-project/blob/master/demo.png>

<h2> Architecture </h2>
We will use MariaDB to create just one database: "spending", one table: "bill", and five columns for the table "bill": "Year", "Month", "Name", "Amount", "Paid".

Next we will setup the datasource in Grafana to use the DB above and create MySQL queries to generate the reports.
 
In my dashboard, I defined three variables: Year, Month, and Name, which can be selected to for some speific queries.

The dashboard will have three rows:

Row 1 is for the monthly spending for the selected "Month", and will be consist of three columns: the total monthly spending, the monthly spending pie chart, and a graphical comparison of the selected "Month" with the previous year's "Month".

Row 2 is for the yearly spending for the selected "Year", and will also consist of three columns: the total yearly spending, the yearly spending pie chart, and a comparison with last year's spending.

Row 3 is for the spending of the selected item in the "Name" field,  and will yet again consist of three columns: the total yearly spending of the item selected from the top, the monthly spending of the selected item, and a comparison with data of the same month from last year.

I uploaded my dashboard as "Expense.json", which is can be found above with the sample-data.

Here are the main steps for setting-up MariaDB and Grafana on Centos 8:

<h2>1. Install / Config Mariadb</h2>

 
 1.1 install mariadb, for CentOS 8 run

    dnf install -y mariadb
   
   for Mac OS
    
    brew install mariadb

 1.2 setup / config mariadb, for CentOS 8 run

     systemctl enable --now mariadb
     mysql_secure_installation
     
   for macOS run
     
     "mysql.server start"
     
 1.3 create database and read only user for Grafana

     mysql -u root -pxxxxx
      >create database spending;
      >CREATE USER 'grafanaRO'@'localhost' IDENTIFIED BY 'mypassword';
      >GRANT SELECT ON spending.* TO 'grafanaRO'@'localhost';
     
      
 1.4 download the sample-data

    curl https://raw.githubusercontent.com/Miker48/Expense-project/master/sample-data > sample-data
 
 1.5 create table and load sample data
 
    mysql -uroot -pxxxxx spending <<EOF
    create table if not exists bill ( Year char(4) NOT NULL, Month char(3) NOT NULL, Name varchar(25) NOT NULL, Amount float(10,2) NOT NULL, Paid varchar(3) NOT NULL, PRIMARY KEY (Year, Month, Name));
    load data local infile 'sample-data' into table bill fields terminated by ':';
    EOF


<h2>2. Install / Setup Grafana</h2>

2.1 install grafana server and pie chart plugin, for CentOS8 run the folowing commands as user root

    dnf install grafana
    grafana-cli plugins install grafana-piechart-panel
    
   for macOS run
    
    brew install grafana

2.2 enable / start grafana server, for CentOS 8 run

    systemctl enable --now grafana-server
    
   for macOS run
    
    brew services start grafana

2.3 setup data source

    go to http://localhost:3000

    login as admin/admin

    change password and setup data source
    setting ---> "Data Sources" ---> "Add Data Source" ---> select "MySQL" ---> fill the following fields under "MySQL Connection", 
    "Host" "Database" "User" "Password" ---> Click "Save & Test"

2.4 Download the grafana dashboard code

    curl https://raw.githubusercontent.com/Miker48/Expense-project/master/Expense.json > Expense.json

2.5 import grafana dashboard 

    click "Grafana" icon on the top left ---> Home ---> Import Dashboard ---> upload .json file ---> and select the file dowbloaded above ---> click "Import"
   

