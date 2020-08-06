Normally when people want to generate monthly expense graphic reports, they will use LAMP stack and write large amounts of code in either java, python, PHP, or Perl etc to query the database and draw the graphical reports.
In this project, I am going to use cutting edge softwares, Grafana and MariaDB to make the process simpler and easier.
<img src=https://github.com/Miker48/Expense-project/blob/master/demo.png>

<h2> Architecture </h2>
MariaDB with just one database: "spending", and one table: "bill", and just five columns in the table of "bill": Year, Month, Name, Amount, Paid.

And then we setup datasource in Grafana to use the above DB, and do some MySQL queries to generate the reports.
 
In my dashboard, I have 3 variables: Year, Month, and Name, which can be selected to do some speific queries.

and the dashboard has 3 rows:

Row 1 is for monthly spending for the selected "Month", which has 3  columes: total month spending, month spending pie chart, and comparison with same month of last year.

Row 2 is for yealy spending for the selected "Year", which also has 3 columns: total yearly spending, yealy spedning pie chart, and comparison with last year.

Row 3 is for spending of the selected item in the "Name" field,  which has 3 columes too: total yealy spening of the item we select from the very top, monthly spending of selected item, and and comparison with same month of last year.

I have exported my dashboard and uploaded as "Expense.json" above, and also uploaded sample-data.

Here are the main steps for setting-up MariaDB and Grafana on Centos 8:

<h2>1. Install / Config Mariadb</h2>

 
 1.1 install mariadb, for CentOS 8 run

    "dnf install -y mariadb"

 1.2 setup / config mariadb

     systemctl enable --now mariadb
     mysql_secure_installation
     
 1.3 create database and read only user for Grafana

     mysql -u root -p
      >create database spending
      >CREATE USER 'grafanaReader' IDENTIFIED BY 'mypassword';
      >GRANT SELECT ON spending.* TO 'grafanaReader';
     
      
 1.4 download the sample-data
 
 1.5 create table and load sample data
 
    mysql -uroot -pxxxxx spending <<EOF
    create table if not exists bill ( Year char(4) NOT NULL, Month char(3) NOT NULL, Name       varchar(25) NOT NULL, Amount float(10,2) NOT NULL, Paid varchar(3) NOT NULL, PRIMARY KEY (Year, Month, Name));
    load data local infile 'sample-bill' into table bill fields terminated by ':';
    EOF


<h2>2. Install / Setup Grafana</h2>

2.1 install grafana server and pie chart plugin, for CentOS8 run the folowing commands as user root

    dnf install grafana
    grafana-cli plugins install grafana-piechart-panel

2.2 enable / start grafana server

    systemctl enable --now grafana-server

2.3 setup data source

    go to http://localhost:3000

    login as admin/admin

    change password and setup data source
    setting ---> "Data Sources" ---> "Add Data Source" ---> select "MySQL" ---> fill the following fields under "MySQL Connection", 
    "Host" "Database" "User" "Password" ---> Click "Save & Test"

2.4 import grafana dashboard 

    download Expense.json and import to Grafana dashboard

    click "Grafana" icon on the top left ---> Home ---> Import Dashboard ---> upload .json file ---> and select the file dowbloaded above ---> click "Import"
   

